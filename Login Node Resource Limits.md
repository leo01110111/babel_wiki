# Login Node Resource Limits

Login nodes (e.g., babel-login-1 through babel-login-4) are built for job submission, editing scripts, and checking status; not heavy computing. To keep them fast for everyone, we set resource limits for the `hpcuser` group. These limits aim to balance usability and performance. Intensive tasks can slow things down and impact other users if unchecked. For heavy work, use compute nodes, where limits are lifted.

## Current Limits
The resource limits below apply to `hpcuser` group members on login all nodes. Each has a starting value (soft limit) and a maximum you can raise it to with `ulimit` (hard limit):

| Limit | Soft Limit | Hard Limit | Description |
| --- | --- | --- | --- |
| Max open files | 4096 | 8192 | Covers files, sockets, and pipes per process |
| Max processes | 1000 | 2000 | Limits total processes and threads per user |
| Max locked memory | 8 MB | 16 MB | Amount of RAM a process can lock (rarely used) |
| Total memory | 4 GB | 8 GB | Virtual memory per process (code, data, stack) |
| Resident memory | 2 GB | 4 GB | Physical memory used per process |
| CPU time | 1 hour | 2 hours | Total CPU time per process |

These are sufficient to support tools like VS Code and Cursor in typical scenarios, such as editing code, managing small projects, or running lightweight extensions. However, they may not handle all use cases. Heavy workloads—like running VS Code with numerous resource-intensive extensions (e.g., TensorBoard, large language model plugins), opening massive workspaces with thousands of files, or processing big datasets—can push beyond these limits, especially for memory or file descriptors.

## Checking Your Usage
Use these commands to see how close you are to your limits. Each pair shows your current usage and the limit:

| Resource | Check Usage | See Limit | What It Means |
| --- | --- | --- | --- |
| Processes | ps -u $USER -o pid= \| wc -l | ulimit -u | Number of processes/threads (e.g., 500 of 1000) |
| Open Files | lsof -u $USER \| wc -l | ulimit -n | Open files or sockets (e.g., 1024 of 4096) |
| Locked Memory | cat /proc/self/status \| grep VmLck | ulimit -l | Locked RAM in KB (e.g., "VmLck: 2048 kB" = 2 MB of 8 MB) |
| Total Memory | pmap $$ \| tail -n 1 \| awk '{print $2}' | ulimit -v | Virtual memory in KB (e.g., 225628 = ~225 MB of 4 GB) |
| Resident Memory | pmap $$ \| tail -n 1 \| awk '{print $3}' | ulimit -m | Physical memory in KB (e.g., 6628 = ~6.6 MB of 2 GB) |
| CPU Time | ps -p $$ -o time \| tail -n 1 | ulimit -t | CPU time used vs. limit (e.g., "TIME 00:30:00" = 30 min of 1 hour) |

Run these in your shell. The "Check Usage" column shows what you’re using; "See Limit" shows your soft limit. Hard limits are in `cat /proc/self/limits`.

## Adjusting Limits
You can raise soft limits up to the hard limit using `ulimit` in your session:
- Increase open files: `ulimit -n 8192`
- Increase processes: `ulimit -u 2000`
- Increase locked memory: `ulimit -l 16384` (in KB)
- Increase total memory: `ulimit -v 8388608` (in KB)
- Increase resident memory: `ulimit -m 4194304` (in KB)
- Increase CPU time: `ulimit -t 7200` (in seconds)

These changes last only for your current session. You can’t exceed the hard limits without admin help—use compute nodes for bigger needs.

## Using VS Code on Compute Nodes
VS Code on login nodes can consume a lot of RAM at times (especially with extensions or TensorBoard). To avoid this, request a debug node and run VS Code there instead. For example, type `babel-9-11` (or your babel-nodename) in the "Connect to Host" prompt in VS Code. This connects your VS Code to a compute node, where resource limits are much higher.

To set this up, add the following to your `~/.ssh/config` file:

```
 Host babel
   HostName babel.lti.cs.cmu.edu
   User <username>
   IdentityFile ~/.ssh/<ssh_key>
   ForwardAgent yes
 Host babel-*
   HostName %h
   User <username>
   IdentityFile ~/.ssh/<ssh_key>
   ProxyJump babel
```

Replace `<username>` with your username and `<ssh_key>` with your SSH key file name (e.g., id_rsa). 

See [SSH](SSH.md) for more details on SSH configuration.

See [VSCode](VSCode.md)

## Common Questions
Here are answers to common questions about these limits:

- **Will my processes be killed?**  
Not immediately! If you hit a limit (e.g., open too many files or use too much CPU time), new actions will fail with an error (like "Too many open files" or "Resource temporarily unavailable"). Existing processes won’t be killed unless they try to exceed the limit after it’s reached. Move long-running or resource-heavy tasks to compute nodes to avoid this.

- **Can I increase these limits myself?**  
You can bump soft limits (e.g., from 4096 to 8192 files, 8 MB to 16 MB locked memory, 4 GB to 8 GB total memory) with `ulimit` in your session, but hard limits (e.g., 8192 files, 16 MB, 8 GB, 2 hours CPU) are fixed. For anything bigger, submit a job to a compute node.

- **How do I check my usage?**  
Use `ulimit -a` to see your limits. Check open files with `lsof -u $USER | wc -l`. See memory limits with `cat /proc/self/limits`.

- **What if I need more resources on the login node?**  
Login nodes aren’t built for this. Submit a job to a compute node—they have higher limits.

- **Will my tmux sessions be killed?**  
No. tmux itself uses very little CPU time; like just a few minutes over several hours and should typically be well under the 1-hour CPU limit. If you run lots of commands inside tmux (e.g., 50+ subprocesses or a script using >4 GB), those might fail—or hit the CPU limit after 1 hour—affecting specific panes.

See the [FAQ](FAQ.md) for more commonly asked questions. 

## Need More Resources?
Login nodes aren’t for heavy work. Submit jobs to compute nodes—they have higher limits (e.g., 131072 files, unlimited memory and CPU). Contact ans Admin if you’re stuck.

If these limits feel too tight, let us know your specific needs—we can tweak them to keep things smooth.
