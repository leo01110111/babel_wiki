# FAQ

## FAQ on Babel Resource Allocation and Best Practices
### **Q: How are jobs prioritized by the scheduler?**
Generally, all users have equally priority. Though the debug, general, and long partitions do not have user or group-specific priority, each partition is ranked in priority from high to low. User fairshare is factored into scheduling. In some cases, some users may have higher-priority access; for example, research groups who have donated nodes may request dedicated partitions for priority access to (a subset of) those nodes or may request a dedicated node reserved exclusively for their research group.

### **Q: Do you have advice for long-running jobs?**
1. Make sure your code saves checkpoints frequently so that it can recover from being preempted.
1. Post on the `#babel-babble` Slack channel first to alert other users.
1. Consider running on the `long` partition. 

### **Q: What should I do if I notice another user's jobs/files are disrupting usage of the cluster for others?**
Please message the `babble-babel` channel, tagging the user with the problematic job as well as `@help-babel`. Remember to **communicate with respect**; most errors are honest mistakes.

### **Q: What should I do if a model requires more compute resources?**
Try to allocate more GPUs when you start the Shell session on the assigned compute node.

### **Q: What does it mean if I get an error message saying 'Unable to contact Slurm controller'?**
Something has gone horribly wrong. Contact the system administrators to resolve this problem.

### **Q: How does this relate to front-end and back-end development?**
Deploy both front-end and back-end server on the same compute node to avoid port forwarding issues

### **Q: I have other questions which aren't answered here.**

Reach out on the `babble-babel` Slack channel, tagging in `@help-babel`. If you discover an answer which may be useful to others, please feel free to add to this FAQ.
