# SLURM Job Management

[Category:Monitoring](Category%3AMonitoring.md)
[Category:Slurm](Category%3ASlurm.md)
[Category:Jobs](Category%3AJobs.md)

How to list and track job statuses, view job details, cancel or hold jobs, and efficiently control job scheduling and execution within an HPC cluster environment. 

## Information on Jobs

To access information about jobs in the cluster, you can use various commands:

List all current jobs for a user:
```
squeue -u <username>
```

List all running jobs for a user:
```
squeue -u <username> -t RUNNING
```

List all pending jobs for a user:
```
squeue -u <username> -t PENDING
```

List priority order of jobs for the current user (you) in a given partition:
```
showq-slurm -o -u -q <partition>
```

List all current jobs in the shared partition for a user:
```
squeue -u <username> -p shared
```

List detailed information for a job (useful for troubleshooting):
```
scontrol show jobid -dd <jobid>
```

List status info for a currently running job:
```
sstat --format=AveCPU,AvePages,AveRSS,AveVMSize,JobID -j <jobid> --allsteps
```

Once your job has completed, you can get additional information that was not available during the run. This includes run time, memory used, etc.

To get statistics on completed jobs by jobID:
```
sacct -j <jobid> --format=JobID,JobName,MaxRSS,Elapsed
```

To view the same information for all jobs of a user:
```
sacct -u <username> --format=JobID,JobName,MaxRSS,Elapsed
```

## Controlling Jobs

To cancel one job:
```
scancel <jobid>
```

To cancel all the jobs for a user:
```
scancel -u <username>
```

To cancel all the pending jobs for a user:
```
scancel -t PENDING -u <username>
```

To cancel one or more jobs by name:
```
scancel --name myJobName
```

To hold a particular job from being scheduled:
```
scontrol hold <jobid>
```

To release a particular job to be scheduled:
```
scontrol release <jobid>
```

To requeue (cancel and rerun) a particular job:
```
scontrol requeue <jobid>
```
