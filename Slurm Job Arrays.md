# Slurm Job Arrays

[Category:Slurm](Category%3ASlurm.md)
[Category:Jobs](Category%3AJobs.md)

## Job Array Scheduling
[Job Array Scheduling](https://slurm.schedmd.com/job_array.html) can be useful when you desire to run multiple jobs but want to use more than *`N`* resources at a time. This is a way to tell slurm to setup a queue for the jobs with only 10 being active at a time ?

  #!/bin/bash
  #SBATCH --array=1-50%10
  #SBATCH --max-concurrent=10
  
  #SBATCH [other options]
  
  # Your job commands here

Explanation of the options:

`--array=1-50%10`: This option defines the job array range from 1 to 50. The %10 specifies that a maximum of 10 tasks from the array can be running concurrently at any one time. Slurm will automatically manage the scheduling and execution of the array tasks based on the specified limit.

`--max-concurrent=10`: This option sets the maximum number of concurrently running tasks within the job array to 10. Slurm will ensure that only 10 tasks are active simultaneously, automatically scheduling the remaining tasks as earlier tasks complete.

By using the --array option with the specified range and the %10 syntax, combined with the --max-concurrent option, you can control the number of tasks running concurrently within the job array. Slurm will handle the scheduling and ensure that only the specified maximum number of tasks are active at any given time.
