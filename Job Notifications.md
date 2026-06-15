# Job Notifications

[Category:Slurm](Category%3ASlurm.md)
[Category:Jobs](Category%3AJobs.md)

How to make a SLURM batch job notify you (push / email / text) when it
begins, ends, or fails.

## Does native SLURM email work on Babel?

**Probably not.** Babel is configured with a mail program but the underlying
mail binary is missing:

- `scontrol show config` shows `MailProg = /usr/bin/smail` (a Princeton
  helper script) and no `MailDomain`.
- `smail` works by piping a `seff` report into `/bin/mail`. But `/bin/mail`
  does not exist on the login node, and there is no `mailx` / `sendmail` /
  `postfix` / outbound relay anywhere on `PATH`.
- Result: `--mail-type` events fire but almost certainly deliver nothing.

Before relying on native email, send yourself a test and confirm it actually
arrives (check spam too):

```bash
sbatch --partition=debug --gres=gpu:1 --time=00:01:00 \
       --mail-type=ALL --mail-user=you@example.com \
       --wrap="echo test; sleep 10"
```

If the message lands, native mail works for you and the directives below are
all you need. If it doesn't, use the webhook method instead.

## Method 1: native SLURM email (only if test delivery confirmed)

Add to your sbatch script:

```bash
#SBATCH --mail-type=END,FAIL          # or BEGIN,END,FAIL,TIME_LIMIT_90 / ALL
#SBATCH --mail-user=you@example.com
```

`--mail-type` values: `BEGIN`, `END`, `FAIL`, `REQUEUE`, `ALL`,
`TIME_LIMIT_90` (90% of time limit reached), `TIME_LIMIT`.

## Method 2: text message via email-to-SMS gateway

Only works if native mail works. Mail your carrier's gateway instead of an
inbox:

```bash
#SBATCH --mail-user=5551234567@vtext.com   # Verizon
# AT&T: @txt.att.net   T-Mobile: @tmomail.net
```

## Method 3: webhook push (recommended — independent of cluster mail)

Compute nodes have outbound HTTPS, so a `curl` to a push service such as
[ntfy.sh](https://ntfy.sh) works regardless of the broken mail setup.
Subscribe once in the ntfy phone app (or at `https://ntfy.sh/<your-topic>`),
then append a notify block to the **end** of your sbatch script:

```bash
# --- notify on completion ---
status=$?
topic="my-babel-jobs"   # any hard-to-guess name; ntfy topics are PUBLIC
msg="$SLURM_JOB_NAME ($SLURM_JOB_ID) on $(hostname): exit $status"
if [ $status -eq 0 ]; then
    curl -s -H "Title: Job OK"     -d "$msg" "ntfy.sh/$topic" >/dev/null
else
    curl -s -H "Title: Job FAILED" -H "Priority: high" -d "$msg" "ntfy.sh/$topic" >/dev/null
fi
```

Place the block right after your main `srun`/training command so `$?` is the
job's real exit code. Slack and Discord incoming webhooks work the same way.

### Catching cancellations and timeouts

A trailing `curl` only runs if the script reaches the end — a `scancel`ed job
or one that hits its `--time` limit may skip it. Trap the termination signal
to notify on those cases too:

```bash
#SBATCH --signal=B:TERM@120   # SIGTERM 120s before the time limit

notify() { curl -s -H "Title: Job ending" -d "$SLURM_JOB_NAME ($SLURM_JOB_ID) caught signal" "ntfy.sh/my-babel-jobs" >/dev/null; }
trap notify TERM
```

## See also

- [Slurm](Slurm.md)
- [SLURM Job Management](SLURM%20Job%20Management.md)
- [Seff](Seff.md)
