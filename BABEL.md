# BABEL

[Category:Babel](Category%3ABabel.md)
[Category:Clusters](Category%3AClusters.md)
[Category:LTI](Category%3ALTI.md)
[Category:Datasets](Category%3ADatasets.md)
# Welcome to the Babel HPC Cluster!
Babel is a high-performance computing (HPC) cluster designed to deliver advanced computational capabilities for research and scientific tasks. This wiki page offers details on the cluster’s architecture and specifications, alongside resources to help you get started. To make the most of Babel’s high-performance computing resources, please review our documentation for job submission guidelines, available software, and best practices. If you have questions, don’t hesitate to contact our support team or explore the user forums.

Happy computing! 

## Getting Started:
- **[Account Requests](https://forms.gle/eptScAsDudyxF8956):** Use this form to request an account or for requesting changes to an existing account.
- **[Slack:](BABEL.md#getting_help)** We have a healthy and active Slack channel, #babel-babble, where you can ask questions, share insights, and stay updated on system announcements.
- **[Login:](#how-to-login-to-the-cluster)** Securely connect to the cluster.
- **[Filesystem:](#filesystem)** Understand where to store your data, models, and personal directories.
- **[Submitting Jobs:](#submitting-jobs)** How to schedule and manage your computational tasks using the resource scheduler.
- **[Getting Help:](#getting-help)** How to seek access to resources, request account changes requests, and general help guildlines.

## How to Login to the Cluster
From your [SSH](SSH.md) client, use the command:
```
 ssh <username>@login.babel.cs.cmu.edu
```

- **Credentials:** Use your Andrew ID and password; SCS credentials have no power here.

There are 4 login nodes in a round-robin DNS setup. You'll be directed to one of these nodes randomly upon connection. If you land on a node other than where your tmux session is active (e.g., you're on login3 but your session is on login2), you can SSH directly to the correct node:
```
 ssh login2
```

More about [SSH](SSH.md)

See also: [Login Node Resource Limits](Login%20Node%20Resource%20Limits.md)

## Shell Configuration

If you would like to set or change your shell on all nodes of the cluster, including login and compute nodes, please submit your request via the [Change Request Form](https://forms.gle/eptScAsDudyxF8956).

If you require a different shell than you find in the drop=-down list, please add it to the notes and we'll do our best to accomodate.

## Filesystem

### Layout
Each user is provisioned with:

```
          /home/<username>: 100GB capacity; mounted on all nodes
/data/user_data/<username>: 500GB capacity; available only on compute nodes with active jobs
```

Additional paths available on each compute node:

```
      /data/datasets: Community datasets.
        /data/models: Community models.
            /scratch: Local SSD or NVMe storgae; when greater than 65% full data older than 28 days is automatically expunged.
/compute/<node_name>: Each nodes /scratch dir is exported to every other node.
```

**Note:**  Home, user_data, datasets, and models are network data directories, mounted via [AutoFS](Filesystem.md#automount_via_autofs) which is an “on-demand” filesystem. You may need to stat the full path to the trigger the mount mechanism. See Filesystem - Automount for more details.

For more details see: [Filesystem#Filesystem Layout](Filesystem.md#filesystem-layout).

### Automount via AutoFS
Non-OS data directories are mounted with AutoFS, which is an "on-demand" filesystem. This means that if you do not put a full path, the data directory will not mount and the higher level directory may appear empty. For a more detailed explanation with examples, please read the Filesystem - Automount wiki entry located at [AutoFS](Filesystem.md#automount-via-autofs).

Information about login nodes can be found here: [Login Node Resource Limits](Login%20Node%20Resource%20Limits.md)

## Submitting Jobs
### Resource Scheduler
[Slurm](Slurm.md) 24.05.1 is used for job scheduling.

There are **2** main ways to request resources:

- **Interactive:** Use `srun` for jobs where you need direct interaction with the running task, often after using `salloc` for interactive sessions.
- **Batch:** Use `sbatch` for jobs that can run without user interaction, typically for longer or resource-intensive tasks, submitting them to the Slurm queue for scheduled execution.
Here's an overview of our main partitions:

- **debug**

```
Purpose: Quick, short jobs for testing and debugging.
      Max Time: 12 hours
  Default Time: 1 hour
      Max GPUs: 2
      Max CPUs: 64
           QoS: debug_qos
   Limitations: No array jobs
```

- **general**

```
Purpose: General, standard computing tasks.
      Max Time: 2 days
  Default Time: 6 hours
      Max GPUs: 8
      Max CPUs: 128
           QoS: normal
   Limitations: No interactive sessions | sbatch only
```

- **preempt**

```
Purpose: Long-running jobs that can be preempted for higher priority tasks.
      Max Time: 31 days
  Default Time: 3 hours
      Max GPUs: 24
      Max CPUs: 256
           QoS: preempt_qos
   Limitations: No interactive sessions | sbatch only
```

- **cpu**

```
Purpose: CPU-only computing tasks.
      Max Time: 2 days
  Default Time: 6 hours
      Max GPUs: 0
      Max CPUs: 128
           QoS: cpu_qos
   Limitations: No interactive sessions | sbatch only
```

- **array**

```
Purpose: Array jobs for parallel task execution.
      Max Time: 12 days
  Default Time: 6 hours
      Max GPUs: 8
      Max CPUs: 256
           QoS: array_qos
   Limitations: No interactive sessions | sbatch only
```

### Partition Table

```
      Name    MaxTRESPU MaxJobsPU MaxSubmitPU  MaxTRES     MinTRES                Preempt
----------- ------------ --------- ----------- -------- ----------- ----------------------
     normal   gres/gpu=8        10          50  cpu=128  gres/gpu=1  array_qos,preempt_qos
preempt_qos  gres/gpu=24        24         100  cpu=256  gres/gpu=1
  debug_qos   gres/gpu=2        10          12   cpu=64                        preempt_qos
    cpu_qos   gres/gpu=0        10          50  cpu=128                        preempt_qos
  array_qos   gres/gpu=8       100       10000  cpu=256                        preempt_qos
```

### Viewing Partition Details

To explore the full configuration of all partitions, use the `scontrol` command:

- `scontrol show part`: Displays detailed information about all available partitions.

For specifics on a particular partition, include its name:

- `scontrol show part <partition_name>`: Shows detailed settings for the specified partition (e.g., `scontrol show part debug`).

For detailed information on how to use these partitions, see our documentation [here](Slurm.md).

### Viewing QoS Details
Each partition is associated with a specific QoS (e.g., `debug_qos`, `normal`, `preempt_qos`), which defines rules such as maximum resource usage and preemption behavior.

To view QoS information associated with your user account:
  
```
 sacctmgr show user $USER withassoc format=User,Account,DefaultQOS,QOS%4
```

## Getting Help
We focus on providing robust computing infrastructure rather than individual software support. 

- [Slack](https://lti-at-cmu.slack.com/archives/C05HRL547MX) is our first line of support. Post issues in the **#babel-babble** channel; to get the admins' attention, tag us with @help-babel. If privacy is needed, send a direct message (DM).
- [User Change Request](https://forms.gle/eptScAsDudyxF8956): Use this form for requesting changes to your account, including additional groups, changing your default shell, and requesting increases in disk capacity.

#### What We Won't Do
- IDE Support: [VSCode](VSCode.md), PyCharm, Jupyter Notebook, et cetera.
- Troubleshoot / Debug Code: Your code is your own.

If you think there may be an issue with our environment that is preventing your code from running let us know.

### Additional Resources:

[Tips and Tricks:](Tips%20%26%20Tricks.md) Configs and best practices from our community.

Request Forms: 
- [|User Change Request:](https://forms.gle/eptScAsDudyxF8956) Use this form to request changes to your account; additional groups, default shell, and increases in disk capacity.
- [link|Dataset Request:]

[Environment Modules:](Environment%20Modules.md)  Modules to modify your user environment

[Tips and Tricks](Tips%20%2526%20Tricks.md)

[Hugging Face Cache Configuration](Hugging%20Face.md)

### FAQ
- [FAQ](FAQ.md)

## Data Collection
[Cluster-Wide Lightweight Instrumentation Profiler](Cluster-Wide%20Lightweight%20Instrumentation%20Profiler.md)

## Upgrade History
- Birth: **2023-05-04 18:02:55.854769612 -0400**
- Hydra9: **2024-10-15 16:35:37.128177207 -0400**
- Migration to MSA: **2025-10-22 10:34:01.158642535 -0400**
