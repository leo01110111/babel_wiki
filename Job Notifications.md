# Job Notifications

[Category:Slurm](Category%3ASlurm.md)
[Category:Jobs](Category%3AJobs.md)

How to make a SLURM batch job notify you (push / email / text) when it
begins, ends, or fails.

## Does native SLURM email work on Babel?

**Yes — confirmed working** (tested 2026-06-15, job 8356445; the email
arrived). Babel uses `MailProg = /usr/bin/smail` (a Princeton helper that
pipes a `seff` report into the message body), and the SLURM controller node
relays it out fine. Note that `/bin/mail` is missing on the *login* node, so
you cannot send mail by hand from there — but that does not affect
`--mail-type`, which runs on the controller.

To sanity-check delivery on your own account (check spam too):

```bash
sbatch --partition=debug --gres=gpu:1 --time=00:01:00 \
       --mail-type=ALL --mail-user=you@example.com \
       --wrap="echo test; sleep 10"
```

## Method 1: native SLURM email (recommended — confirmed working)

Add to your sbatch script:

```bash
#SBATCH --mail-type=END,FAIL          # or BEGIN,END,FAIL,TIME_LIMIT_90 / ALL
#SBATCH --mail-user=you@example.com
```

`--mail-type` values: `BEGIN`, `END`, `FAIL`, `REQUEUE`, `ALL`,
`TIME_LIMIT_90` (90% of time limit reached), `TIME_LIMIT`.

## Method 2: text message via email-to-SMS gateway

Since native mail works, you can get a **text** by mailing your carrier's
gateway instead of an inbox:

```bash
#SBATCH --mail-user=5551234567@vtext.com   # Verizon
# AT&T: @txt.att.net   T-Mobile: @tmomail.net
```

## Method 3: webhook push (alternative — phone push notifications)

If you prefer a phone push instead of email, compute nodes have outbound
HTTPS, so a `curl` to a push service such as [ntfy.sh](https://ntfy.sh)
works.
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
