# Seff

[Category:Monitoring](Category%3AMonitoring.md)
[Category:Slurm](Category%3ASlurm.md)
[Category:Jobs](Category%3AJobs.md)

# seff

## Overview
**seff** (Slurm Efficiency) is a command used in Slurm Workload Manager to report the efficiency of a job. It calculates the CPU and memory efficiency based on the resources requested and the resources used by a job.

## Usage
To use **seff**, run:

```bash
seff [job_id]
```

Where [job_id] is the ID of the Slurm job.

## Output
The output of **seff** includes:
- CPU Efficiency: Percentage of the allocated CPU that was actually used.
- Memory Efficiency: Percentage of the allocated memory that was actually used.

## Examples

```bash
% seff 81080

Job ID: 81080
Cluster: babel-cluster
User/Group: dvosler/dvosler
State: COMPLETED (exit code 0)
Nodes: 1
Cores per node: 4
CPU Utilized: 00:19:55
CPU Efficiency: 98.92% of 00:20:08 core-walltime
Job Wall-clock time: 00:05:02
Memory Utilized: 1.74 GB
Memory Efficiency: 86.85% of 2.00 GB
```

## See Also
- [Slurm](Slurm.md)
- [SLURM Job Management](SLURM%20Job%20Management.md)
