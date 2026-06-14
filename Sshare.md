# Sshare

[Category:Monitoring](Category%3AMonitoring.md)
[Category:Slurm](Category%3ASlurm.md)
[Category:Jobs](Category%3AJobs.md)

[sshare](https://slurm.schedmd.com/sshare.html) is a command-line tool in the SLURM workload manager, used for displaying historical resource usage and fair-share information for users and groups. This information is crucial for understanding how SLURM allocates resources based on past usage, aiding in efficient job scheduling.

## Key Features
- `Account/User Share Information`: Displays the allocation of resources to users and accounts.
- `Raw and Normalized Shares`: Shows both actual and proportionate shares.
- `Usage Data`: Details resource consumption, aiding in monitoring and planning.
- `Fair-share Factor`: Indicates the priority based on usage and allocation. (Higher is better)

## Usage
The basic syntax for **sshare** is:

```bash
sshare [options]
```

Common options include:

- `-a` or `--all`: Show all users/groups.
- `-u` or `--user=[users]`: Display information for specific user(s).
- `-A` or `--account=[accounts]`: Display information for specific account(s).
- `-l` or `--long`: Show detailed information.

## Viewing Your Fair-Share Value
To view your fair-share value, simply run:

```bash
sshare -u <username>
```

Replace `<username>` with your actual username.

## Checking Group Usage
To check the usage for a specific group:

```bash
sshare -A <group_name>
```

Replace `<group_name>` with the name of the group you want to check.

## Detailed Information
For more detailed information, including fields like **RawUsage**, **EffectvUsage**, **NormUsage**, and **FairShare**:

```bash
sshare -l
```

## Examples

1. **View All User Usage:**

```bash
sshare -a
```

This command lists fair-share and usage information for all users.

1. **Specific User in Detail:**

```bash
sshare -l -u jdoe
```

Replace **jdoe** with the username. This provides detailed usage information for user **jdoe**.

1. **Multiple Users:**

```bash
sshare -u "user1,user2"
```

This shows the information for **user1** and **user2**.

## Conclusion
Understanding **sshare** outputs allows users to better comprehend how their and their group's historical usage impacts current and future resource allocation, leading to more efficient job submissions and scheduling on SLURM clusters.

## See Also
- [seff](seff.md)
- [Slurm](Slurm.md)
- [SLURM Job Management](SLURM%20Job%20Management.md)
