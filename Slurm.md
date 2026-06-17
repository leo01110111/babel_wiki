# Slurm

[Category:Slurm](Category%3ASlurm.md)
[Category:Jobs](Category%3AJobs.md)

[SLURM](https://slurm.schedmd.com) (Simple Linux Utility for Resource Management) is a job scheduler and resource management system commonly used in high-performance computing (HPC) environments. It allows users to submit and manage jobs on clusters or supercomputers. This guide provides a brief overview of SLURM and covers basic usage examples for `sbatch` and `srun` commands, along with common options for requesting resources such as memory, CPUs, and GPUs.

For more information on Slurm please consult the [Slurm documentation](https://slurm.schedmd.com/tutorials.html)

## Introduction to SLURM

SLURM is a flexible and efficient framework for job submission and scheduling in HPC environments, enabling users to run parallel and distributed applications across multiple compute nodes in a coordinated manner. It serves as a job scheduler that queues, allocates, and launches both interactive and batch jobs.

Slurm provides two different types of jobs:

- `Interactive Jobs`: Access a compute node like you would via ssh
- `Batch Jobs`: Launch a job in the background

All jobs in the Slurm system are scheduled in a priority queue, meaning that heavy users may need to wait longer for their jobs to be launched. To manage jobs in the system, users can take advantage of several useful Slurm commands, including:

    - `srun`: Run a real time job, useful for launching interactive jobs
    - `sbatch`: Queue a batch job to be launched as available
    - `scancel`: Kill a running slurm job
    - `sacct`: View the history of your recently run jobs; Did they complete? fail?
    - `sinfo`: View the resources, nodes, gres on the slurm cluster
    - `squeue`: View the state of currently running jobs
    - `sstat`: Resource utilization by a particular job

These commands provide users with powerful tools to manage their jobs on the BABEL cluster and ensure that their work is completed efficiently and effectively. We will discuss these commands in more detail later.

# Submitting Jobs
## Submitting Jobs with sbatch

To submit a batch job using `sbatch`, create a shell script (e.g., `job_script.sh`) that contains the necessary commands and configurations for your job. Then, use the following command to submit the job:

```
 $ sbatch job_script.sh
```

SLURM will assign a unique job ID to your job and enqueue it for execution. You can monitor the status of your job using various SLURM commands like `squeue` or `sacct`.

## Submitting Jobs with srun

For interactive or non-batch jobs, you can use the `srun` command. It allows you to execute commands directly on compute nodes. Here’s an example:

```
 $ srun -n 4 ./my_program
```

SLURM will assign a unique job ID to your job and enqueue it for execution. You can monitor the status of your job using various SLURM commands like `squeue` or `sacct`.

The `-n` option specifies the number of tasks you want to run. In the example above, we are running the `my_program` executable on four tasks.

## Basic sbatch Job Submission

An `sbatch` script typically includes directives for resource requests and the application commands. Here’s a minimal example:

```bash
#!/bin/bash
#SBATCH --job-name=my_job
#SBATCH --partition=preemptable_partition
#SBATCH --time=2:00:00  # Max job run time
#SBATCH --cpus-per-task=4
#SBATCH --mem=16G

# Load any required modules
module load your_application_module

# Run the application
srun ./your_application
```

Save this as `job_script.sh` and submit it with:

```
 $ sbatch job_script.sh
```

## Handling Preemption with Checkpointing

In clusters with preemption set to `requeue`, jobs may be interrupted and requeued, restarting from scratch. Checkpointing allows a job to save its state at intervals, so it can resume from the last checkpoint instead of starting over.

### Implement Checkpointing in Your Application

Your application should support saving its state periodically (checkpoints). This often involves using signals (like `SIGUSR1`) to trigger checkpointing.

### Set Up Checkpointing in Slurm

Specify the `--requeue` option to ensure that the job will be requeued upon preemption:

```bash
#SBATCH --requeue
```

### Use a Checkpoint Signal

Configure Slurm to send a checkpoint signal (e.g., `SIGUSR1`) at a defined interval:

```bash
#SBATCH --signal=B:USR1@60
```

This directive sends `SIGUSR1` to your job 60 seconds before it is preempted. Adjust the timing based on your checkpointing needs.

### Handle Checkpoint Signal in the Script

Set up a trap in your `sbatch` script to call the checkpoint function when `SIGUSR1` is received:

```bash
trap 'checkpoint' USR1

checkpoint() {
    # Replace this with your application's checkpoint command
    ./your_application --checkpoint
}
```

### Resume from Checkpoint in the Job Script

When the job is requeued, configure the job to restart from the last saved checkpoint:

```bash
if [ -f checkpoint_file ]; then
    srun ./your_application --resume checkpoint_file
else
    srun ./your_application
fi
```

## Final Example Script

Here is a complete example of an `sbatch` script that uses checkpointing:

```bash
#!/bin/bash
#SBATCH --job-name=my_checkpointed_job
#SBATCH --partition=preemptable_partition
#SBATCH --time=2:00:00
#SBATCH --ntasks=1
#SBATCH --gpus=2
#SBATCH --output=/home/dvosler/logs/preemption_test-%j.log
#SBATCH --error=/home/dvosler/logs/error-%j.out
#SBATCH --mail-type=END
#SBATCH --mail-user=dvosler@cs.cmu.edu
#SBATCH --requeue  # Allows the job to be requeued after preemption
#SBATCH --signal=B:USR1@60

# Define a checkpoint file with job ID in the logs directory
CHECKPOINT_FILE="/home/dvosler/logs/checkpoint-${SLURM_JOB_ID}.txt"

# Load checkpoint if it exists
if [[ -f $CHECKPOINT_FILE ]]; then
    i=$(cat $CHECKPOINT_FILE)
    echo "Resuming from iteration $i"
else
    i=1
    echo "Starting fresh"
fi

hostname;
date;
nvidia-smi -L;

# Simulate work with checkpointing
for (( ; i<=1000; i++ )); do
    echo "Iteration $i"
    sleep 5  # Simulate work

    # Save progress
    echo $i > $CHECKPOINT_FILE
done

echo "Job completed at $(date)"
rm -f $CHECKPOINT_FILE  # Clean up checkpoint file on completion
```

This setup allows Slurm to preempt and requeue the job without losing progress. The application resumes from the latest checkpoint file if preempted. Adjust checkpoint frequency and requeue timing based on application requirements.

# Requesting Resources

SLURM provides options to request specific resources for your jobs, such as memory, CPUs, and GPUs.

## Memory

To request a specific amount of memory for your job, use the `--mem` option with the desired value. For example, to request 8 GB of memory, use:

```
 $ #SBATCH --mem=8G
```

## CPUs

SLURM allows you to request a specific number of CPUs for your job. Use the `--cpus-per-task` option to specify the number of CPUs needed. For example, to request 4 CPUs per task, use:

```
 $ #SBATCH --cpus-per-task=4
```

## GPUs

If your job requires GPU resources, you can request them using the `--gres` option. For example, to request 2 GPUs, use:

```
 $ #SBATCH --gres=gpu:2
```
You can also specify the specific GPU type using the `--gres` option. For example, to request 2 NVIDIA V100 GPUs, use:

```
 $ #SBATCH --gres=gpu:v100:2
```

## Time

To request a specific runtime for your job, use the `--time` option with the desired duration. Specify the time in the format `DAYS-HOURS:MINUTES:SECONDS`. For example, to request 2 hours, use:

    #SBATCH --time=2:00:00

For 1 day and 12 hours, use:

    #SBATCH --time=1-:12:00:00

## Sample Jobs

### SBATCH

To submit a batch job using SBATCH, you need to create a job script file. Here's an example of a simple SBATCH job script:

 #!/bin/bash
 #SBATCH --job-name=myjob
 #SBATCH --output=myjob.out
 #SBATCH --error=myjob.err
 #SBATCH --partition=general
 #SBATCH --nodes=1
 #SBATCH --ntasks-per-node=1
 #SBATCH --cpus-per-task=1
 #SBATCH --time=1:00:00
 
 # Your job commands go here
```
echo "Hello, World!"
```

In this example, the job script starts with specifying the job name, output file, error file, partition, number of nodes, tasks per node, CPUs per task, and maximum runtime. You can modify these parameters based on your job requirements. The actual commands of your job follow after the SBATCH directives.

To submit the job, use the following command:

```
sbatch myjob.sh
```

This command submits the job script file "myjob.sh" to the Slurm scheduler.

### SRUN

If you prefer running jobs directly with SRUN without a batch job script, you can use the following command:

```
 srun --job-name=myjob --partition=compute --nodes=1 --ntasks-per-node=1 --cpus-per-task=1 --time=1:00:00 echo "Hello, World!"
```

In this example, the job name, partition, number of nodes, tasks per node, CPUs per task, and maximum runtime are specified as command-line options. The "echo" command is the actual job command that will be executed.

Feel free to modify the parameters and the job command according to your specific needs.

### ARRAY JOBS

Slurm array jobs allow submitting multiple similar jobs with a single submission. Here’s an example of an array job script:

```
#!/bin/bash
#SBATCH --job-name=array_job
#SBATCH --output=outputs/job_%A_%a.out
#SBATCH --error=errors/job_%A_%a.err
#SBATCH --partition=array
#SBATCH --nodes=1
#SBATCH --ntasks-per-node=1
#SBATCH --cpus-per-task=1
#SBATCH --time=1:00:00
#SBATCH --array=0-9    # Submit 10 jobs with indices from 0 to 9

echo "Running task $SLURM_ARRAY_TASK_ID"

# Run a command using the task ID
python my_script.py --task_id=$SLURM_ARRAY_TASK_ID
```

In this example:
- The `#SBATCH --array=0-9` directive submits 10 independent tasks.

- `%A` in the output/error filenames represents the master job ID.

- `%a` represents the task ID within the array.

- The `SLURM_ARRAY_TASK_ID` environment variable is used to differentiate tasks.

To submit the array job:
```
sbatch array_job.sh
```

To check the status of array jobs:
```
squeue --user=$USER --array
```

You can also limit how many tasks run simultaneously by using:
 #SBATCH --array=0-9%2  # Only 2 jobs run at a time

This helps manage resource usage and avoid job contention.

## Slurm Partitions Overview

### Debug
- **Default Time:** 01:00:00
- **Max Time:** 12:00:00
- **Priority Job Factor:** 8000

### General
- **Default Time:** 06:00:00
- **Max Time:** 2-00:00:00
- **Priority Job Factor:** 6000

### Long
- **Default Time:** 1-00:00:00
- **Max Time:** 7-00:00:00
- **Priority Job Factor:** 5000

### CPU
- **Default Time:** 12:00:00
- **Max Time:** 2-00:00:00
- **Priority Job Factor:** 7000

---

Additional partitions are available. For more detailed information, users can use `sinfo` and `scontrol` commands to query the cluster configuration.

# Tips and Tricks

## Job Options

Here are some additional SLURM directives commonly which may make SLURM life more pleasant:

- `--output=/home/<username>/path/output_report-%j.out`: Specifies the path and filename pattern for the job’s standard output and error logs. `%j` is a placeholder that will be replaced with the job ID.
- `--mail-type=END`: Specifies the email notification types for job events. In this case, it is set to receive an email when the job ends.
- `--mail-user=<username>@andrew.cmu.edu`: Specifies the email address where the job-related emails will be sent.

Here are other useful arguments for `srun/sbatch`:

- `-w, --nodelist`: Nodes to run jobs (`-x, --exclude` is the opposite)
- `-t, --time`: Time limit (D-HH:MM:SS, 0=infinity) [explained mode below in interactive jobs]
- `-o, --output`: Output log files. It’s a good idea to flush output frequently to get timely output.

### List Partitions

To get a list of available partitions in the cluster, you can use the following command:

```
sinfo
```

This command displays information about the partitions, including their names, node counts, and node states. It provides an overview of the available partitions that you can specify in your job submission.

### List Nodes

To get a list of all nodes in the cluster, you can use the following command:

```
scontrol show nodes
```

This command provides detailed information about each node, including their names, states, CPU and memory information, and any associated partitions.

### Node Details

To get specific information about a particular node, you can use the following command, replacing "node-name" with the actual name of the node:

```
scontrol show node node-name
```

This command displays detailed information about the specified node, including its state, CPU and memory information, and any associated partitions.

### Partition Details

To get detailed information about a specific partition, you can use the following command, replacing "partition-name" with the actual name of the partition:

```
scontrol show partition partition-name
```

This command provides information about the specified partition, including its name, node range, state, and other properties.

By using these commands, you can gather essential information about the partitions and nodes in the Slurm cluster, which can be useful for job submission and understanding the cluster's current status.

# See Also
For more information on Slurm please consult the [Slurm documentation](https://slurm.schedmd.com/tutorials.html), which provides detailed tutorials and resources for working with Slurm in an HPC environment.

**[SLURM Job Management](SLURM%20Job%20Management.md): Track and modify your Slurm jobs. **
**[Slurm Job Efficiency](Slurm%20Job%20Efficiency.md): See your past usage.**
**[Slurm Job Arrays](Slurm%20Job%20Arrays.md): Efficiently execute multiple similar jobs with a single job script and varying parameters, simplifying job management and enabling parallel processing**
**[Slurm Submission from Python](Slurm%20Submission%20from%20Python.md): Submit and manage SLURM jobs directly from Python scripts.**
Happy computing with SLURM!
**[seff](seff.md): How to report the efficiency of a job.**
**[sshare](sshare.md): Detailed information about your job priority, resource shares and usage.**
