# Filesystem

This page provides an overview of the storage setup for the Babel HPC cluster, including the filesystem layout, and guidelines for utilizing each path effectively.

## Filesystem Layout

Our cluster's filesystem layout (excluding OS-related directories) is as follows:

- **/data/**
    - **datasets/** - Storage for large datasets used in training, validation, or testing of machine learning models.
    - **group_data/** - Shared storage space for research group collaboration. 
    - **hf_cache** - Cache for Hugging Face Hub files (e.g., pretrained models, datasets, tokenizers). See: [Hugging Face](Hugging%20Face.md)
    - **models/** - Storage for model weights, checkpoints, and final models.
    - **user_data/** - General-purpose storage for user-specific data that needs to persist across jobs. Default allocation is 500 GB.
- **/home/&lt;username&gt;** - Home directories for individual users. Limited to 100 GB of storage.
- **/scratch** - A large, temporary storage volume (5.5T) for job-related data that may persist across multiple job runs or be shared between jobs.
- **/scratch/job_tmp** - A dedicated temporary storage volume (1.5T) for job-specific data managed by the `job_container/tmpfs` plugin.
Home, user_data, group_data, datasets, and models reside on dedicated Network Attached Storage (NAS) servers and are mounted "on-demand" via AutoFS on each compute node.

## Automount via AutoFS
Non-OS data directories (home, user_data, datasets, models, remote compute scratch) are mounted with AutoFS, which is an "on-demand" filesystem. This means that if you do not put a full path, the data directory will not mount and may appear to you as if nothing is there. For example, if you are on a compute note and run the following your output will return empty:
```
ls -lah /data/user_data
```
You must put your full path of your user_data directory to be able to see the contents:
```
ls -lah /data/user_data/<andrew_id>
```
The same logic applies to dataset, model, and remote scratch directories. Your home directory is also mounted the same way, but it is unlikely you will ever notice since it is mounted as soon as you login (Please notify admins if this does not happen). The surest way to reliably access data is to put the full path or cd into the directory in question.
## Scratch Storage Details

### Job Storage and Results
** ** `/scratch/jopb_tmp/` is not yet implemented. Scheduled for a future maintenance period. ** **

### /scratch
- **Purpose:** `/scratch` is an XFS filesystem designed for temporary or intermediate data that may need to persist beyond a single job's lifecycle or be shared between jobs.
- **Usage:** Ideal for large datasets, intermediate results, or outputs that need to be preserved for subsequent jobs or analysis.

### /scratch/job_tmp
- **Purpose:** `/scratch/job_tmp` is an XFS filesystem providing each job with an isolated, high-performance temporary space.
- **Usage:** Used for job-specific temporary files, such as intermediate results or high-speed I/O operations during job execution. Data in `/scratch/job_tmp/$SLURM_JOB_ID` is automatically cleaned up when a job completes.

### Best Practices

- **Monitor Quotas:** Regularly check your quota usage on /scratch/job_tmp to avoid exceeding limits.
- **Clean Up Data:** While /scratch/job_tmp is automatically cleaned up, manually clean up /scratch when data is no longer needed to free up space.
- **Optimize I/O:** For performance-critical operations, prioritize /scratch/job_tmp over /scratch due to its isolated, high-speed nature.

## Quotas on /scratch/job_tmp

To manage disk usage effectively, quotas are enforced on `/scratch/job_tmp`. These quotas apply to both users and groups:

- **User Quotas:**
```
 Soft Limit: 250GB
 Hard Limit: 1TB
 Grace Period: 7 days
```
- **Group Quotas:**
```
 Soft Limit: set per group
 Hard Limit: set per group
 Grace Period: set per group
```

Users or groups exceeding the soft limit will receive a warning and have 7 days to reduce their usage below the soft limit before the hard limit is enforced. To check your quota usage:

```
xfs_quota -x -c 'report -u' /scratch/job_tmp  # For users
xfs_quota -x -c 'report -g' /scratch/job_tmp  # For groups
```

## Usage Guidelines

### General Workflow

Use `/scratch` for large, temporary data that may need to persist across jobs or be shared between jobs.
Use `/scratch/job_tmp` (via job_container/tmpfs) for fast, job-specific temporary data that is automatically cleaned up after job completion.
### Example Job Script
Here’s an example of how to utilize `/scratch`, `/scratch/job_tmp` and `/data/user_data/<username>` in a job script:

```bash
#!/bin/bash
#SBATCH --job-name=large_ml_job
#SBATCH --output=output_%j.txt

# Copy large dataset to job's fast temporary space
cp /scratch/<username>/large_dataset /scratch/job_tmp/$SLURM_JOB_ID/_tmp/

# Run job using fast local storage
./my_ml_program

# Copy results back to /scratch for temporary persistence
cp /scratch/job_tmp/$SLURM_JOB_ID/_tmp/results /scratch/user_name/results_$SLURM_JOB_ID

# Copy results back to /data/user_data/<username> for long-term persistence
rsync -aAvPHxX /scratch/job_tmp/$SLURM_JOB_ID/_tmp/results /data/user_data/<username>/results_$SLURM_JOB_ID
```

[Category:Slurm](Category%3ASlurm.md)
[Category:Babel](Category%3ABabel.md)
[Category:Clusters](Category%3AClusters.md)
[Category:LTI](Category%3ALTI.md)
