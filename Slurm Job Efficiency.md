# Slurm Job Efficiency

[Category:Slurm](Category%3ASlurm.md)
[Category:Jobs](Category%3AJobs.md)

## Slurm Job Efficiency Script

The **Slurm Job Efficiency Script** is a Python script for analyzing resource utilization of completed Slurm jobs. It uses Slurm's `sacct` and `seff` commands to retrieve job data, providing insights into CPU and memory usage, along with optimization recommendations. The script supports filtering by users, job IDs, partitions, and time ranges, and generates detailed or summary reports.

### Overview

This script assists in monitoring and optimizing resource allocation in Slurm-based HPC clusters. It calculates CPU and memory efficiency, identifies under- or over-utilized resources, and outputs a tabular report with a summary of average efficiencies and actionable insights.

### Example Usage

Below are examples of common use cases, assuming the script is saved as `/opt/cluster_tools/babel_contrib/slurm_job_efficiency.py` and run in a Slurm environment.

#### Analyze Jobs for a Specific User

To analyze completed jobs for user `alice` with the default time range (last 1 day):

```
 python3 /opt/cluster_tools/babel_contrib/slurm_job_efficiency.py alice
```

To analyze jobs for user `alice` over the last 7 days or starting from April 1, 2025:

```
 python3 /opt/cluster_tools/babel_contrib/slurm_job_efficiency.py alice -t 7d
 python3 /opt/cluster_tools/babel_contrib/slurm_job_efficiency.py alice -t 2025-04-01
```

**Output** (for `-t 7d`):

```
 Slurm Job Efficiency:
 User: alice
 ╒══════════╤═════════════════╤════════════╤════════════╤════════════════════════════════════════════════╕
 │ JobID    │ JobName         │ CPU Eff    │ Mem Eff    │ Insights                                       │
 ╞══════════╪═════════════════╪════════════╪════════════╪════════════════════════════════════════════════╡
 │ 12345    │ simulation      │ 95.50%     │ 80.20%     │ -                                              │
 ╞══════════╪═════════════════╪════════════╪════════════╪════════════════════════════════════════════════╡
 │ 12346    │ data_proc       │ 45.30%     │ 20.10%     │ Underutilized CPU; Reduce CPU cores            │
 │          │                 │            │            │ Underutilized memory; Reduce memory allocation │
 ╘══════════╧═════════════════╧════════════╧════════════╧════════════════════════════════════════════════╛
 --------------------------------------
 Total Jobs: 2
   Avg CPU Eff: 70.40%, Avg Mem Eff: 50.15%
 Summary:
   - Underutilized CPU; Reduce requested resources or optimize job
   - Underutilized memory; Reduce memory allocation
 --------------------------------------
```

#### Show Detailed Resource Usage

To analyze jobs for user `alice` with detailed resource columns:

```
 python3 /opt/cluster_tools/babel_contrib/slurm_job_efficiency.py alice -r
```

**Output**:

```
 Slurm Job Efficiency:
 User: alice
 ╒══════════╤═════════════════╤════════════╤════════════╤═════════════╤══════════╤══════════╤══════════════════════════════════════════════════════╕
 │ JobID    │ JobName         │ CPU Eff    │ Mem Eff    │ Req Cores   │ Req Mem  │ Max Mem  │ Insights                                             │
 ╞══════════╪═════════════════╪════════════╪════════════╪═════════════╪══════════╪══════════╪══════════════════════════════════════════════════════╡
 │ 12348    │ compute_task    │ 85.00%     │ 95.00%     │ 4           │ 16GB     │ 15.20 GB │ High memory usage; Increase memory to avoid swapping │
 ╘══════════╧═════════════════╧════════════╧════════════╧═════════════╧══════════╧══════════╧══════════════════════════════════════════════════════╛
 --------------------------------------
 Total Jobs: 1
   Avg CPU Eff: 85.00%, Avg Mem Eff: 95.00%
 Summary:
   - High memory usage; Increase memory to avoid swapping
 --------------------------------------
```

#### Analyze Specific Job IDs

To analyze job IDs `12345` and `12346`:

```
 python3 /opt/cluster_tools/babel_contrib/slurm_job_efficiency.py -j 12345,12346
```

**Output**:

```
 Slurm Job Efficiency:
 User: Any
 ╒══════════╤═════════════════╤════════════╤════════════╤════════════════════════════════════════════════╕
 │ JobID    │ JobName         │ CPU Eff    │ Mem Eff    │ Insights                                       │
 ╞══════════╪═════════════════╪════════════╪════════════╪════════════════════════════════════════════════╡
 │ 12345    │ simulation      │ 95.50%     │ 80.20%     │ -                                              │
 ╞══════════╪═════════════════╪════════════╪════════════╪════════════════════════════════════════════════╡
 │ 12346    │ data_proc       │ 45.30%     │ 20.10%     │ Underutilized CPU; Reduce CPU cores            │
 │          │                 │            │            │ Underutilized memory; Reduce memory allocation │
 ╘══════════╧═════════════════╧════════════╧════════════╧════════════════════════════════════════════════╛
 --------------------------------------
 Total Jobs: 2
   Avg CPU Eff: 70.40%, Avg Mem Eff: 50.15%
 Summary:
   - Underutilized CPU; Reduce requested resources or optimize job
   - Underutilized memory; Reduce memory allocation
 --------------------------------------
```

#### Analyze Jobs for a Partition

To analyze jobs for user `alice` on the `general` partition:

```
 python3 /opt/cluster_tools/babel_contrib/slurm_job_efficiency.py alice -p general
```

**Output**:

```
 Slurm Job Efficiency:
 User: alice (general)
 ╒══════════╤═════════════════╤════════════╤════════════╤══════════════════════════════════════════════════════╕
 │ JobID    │ JobName         │ CPU Eff    │ Mem Eff    │ Insights                                             │
 ╞══════════╪═════════════════╪════════════╪════════════╪══════════════════════════════════════════════════════╡
 │ 12348    │ compute_task    │ 85.00%     │ 95.00%     │ High memory usage; Increase memory to avoid swapping │
 ╘══════════╧═════════════════╧════════════╧════════════╧══════════════════════════════════════════════════════╛
 --------------------------------------
 Total Jobs: 1
   Avg CPU Eff: 85.00%, Avg Mem Eff: 95.00%
 Summary:
   - High memory usage; Increase memory to avoid swapping
 --------------------------------------
```

#### Summary-Only Report

To display only summary statistics for user `alice` over the last 1 day:

```
 python3 /opt/cluster_tools/babel_contrib/slurm_job_efficiency.py alice -t 1d -s
```

**Output**:

```
 Slurm Job Efficiency:
 User: alice
 --------------------------------------
 Total Jobs: 3
   Avg CPU Eff: 75.33%, Avg Mem Eff: 68.67%
 Summary:
   - Underutilized CPU; Reduce requested resources or optimize job
   - Underutilized memory; Reduce memory allocation
 --------------------------------------
```

### Explanation of Options

- `-a, --all`: Analyzes all users with completed jobs.
- `-j, --job-id`: Specifies job IDs to analyze (e.g., `-j 12345,12346`).
- `-t, --time`: Sets time range (e.g., `7d` for 7 days, `12h` for 12 hours) or start date (e.g., `2025-04-01` for jobs starting from April 1, 2025). Defaults to `1d` if not specified.
- `-p, --partition`: Filters by partitions (e.g., `-p compute,gpu`).
- `-r, --show-resource-details`: Includes columns for requested cores, memory, and max memory used.
- `-S, --sort-by`: Sorts table by `jobid`, `cpu_eff`, or `mem_eff`.
- `-s, --summary-only`: Shows only summary statistics.
- `--no-user`: Suppresses printing the username.

### Notes

- Invalid job IDs or users trigger warnings or errors, and the script skips them.
- Time ranges can be specified as `Nd` (days), `Nh` (hours), or a date in `YYYY-MM-DD` format.

### See Also
