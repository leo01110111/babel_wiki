# Cluster-Wide Lightweight Instrumentation Profiler

## Cluster-Wide Lightweight Instrumentation (Babel Cluster)

This project deploys a lightweight background profiler on the [Babel](BABEL.md) GPU cluster to measure system-level performance (GPU, CPU, memory, and network utilization). The goal is to understand real workload behavior, identify bottlenecks, and inform future cluster and system design.

The profiler collects only hardware and OS performance counters. It does not access user code, datasets, model parameters, or program outputs.

### Opt-Out (Per Job)
Any job can opt out of profiling by adding the following line to its Slurm submission script:

```
#SBATCH --comment="PROFILER_DISABLE"
```

Opted-out jobs are automatically excluded.

### Low Overhead
- No GPU overhead
- < 0.05% CPU usage (1 s sampling); ~34 MB memory per node
- ~20 MB/day storage per node (gzip, 1 s sampling)

### FAQ

**Does this slow down my job?**
    No. The profiler does not run on GPUs and has negligible CPU, memory, and I/O overhead. It only polls existing system counters.

**Does it see my code or data?**
    No. The profiler cannot access source code, datasets, model weights, or outputs. It only records aggregate system metrics.

**What exactly is collected?**
    System-level metrics such as GPU utilization, memory usage, power, network throughput, CPU utilization, and basic Slurm job metadata (with anonymized usernames).

**Are usernames or identities exposed?**
    No. Usernames are hashed before being recorded.

**Can I disable profiling for my job?**
    Yes. Add `#SBATCH --comment="PROFILER_DISABLE"` to your Slurm script, and your job will be skipped.

**Is profiling always on?**
    The profiler runs in the background on the Babel cluster, but participation is job-selective via the opt-out mechanism.

**Will the profiler change over time?**
    Possibly. Metrics may be added or adjusted to improve accuracy or coverage, while maintaining low overhead and privacy guarantees.
