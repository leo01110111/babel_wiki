# Ganglia

[Category:Monitoring](Category%3AMonitoring.md)
[Category:Web-Based Monitoring](Category%3AWeb-Based%20Monitoring.md)

1. [http://babel-cluster.lti.cs.cmu.edu/ganglia/?r=hour&cs=&ce=&c=babel-cluster.lti.cs.cmu.edu&h=&tab=m&vn=&hide-hf=false&m=load_one&sh=2&z=small&hc=0&host_regex=&max_graphs=0&s=by+na
me Ganglia] for examining per node load and I/O utilization.

[Ganglia](https://ganglia.sourceforge.net|) is an open-source scalable distributed monitoring system for high-performance computing systems such as clusters and Grids. It is carefully engineered to achieve very low per-node overheads and high concurrency. Ganglia is currently in use on thousands of clusters around the world and can scale to handle clusters with several thousand of nodes.

## Features

Ganglia offers several key features that make it a powerful tool for monitoring HPC status:

**Scalability**: Ganglia is designed to handle large-scale deployments with thousands of compute nodes. It utilizes a hierarchical architecture where monitoring data is aggregated at different levels, allowing for efficient management of monitoring data across distributed clusters.

**Real-time Monitoring**: Ganglia provides real-time monitoring capabilities, allowing administrators and users to track the performance of HPC systems as they execute computationally intensive tasks. It collects a wide range of metrics including CPU utilization, memory usage, network traffic, and disk I/O, among others.

**Visualization**: Ganglia offers a web-based interface that provides visual representations of the collected metrics. These visualizations include graphs, charts, and heatmaps, enabling users to quickly identify performance bottlenecks, trends, and anomalies in the system.

**Alerting and Notification**: Ganglia supports configurable alerts and notifications based on predefined thresholds. Users can set up custom alerts to be triggered when certain metrics exceed or fall below specified values. This feature helps ensure that administrators are promptly notified of any critical issues or performance degradation.

**Extensibility**: Ganglia is highly extensible, allowing users to integrate additional monitoring modules or customize existing ones to meet specific monitoring requirements. This flexibility enables seamless integration with various HPC systems and architectures.

## Usage

## Links
- http://atlas.multicomp.cs.cmu.edu/ganglia/
- http://babel-cluster.lti.cs.cmu.edu/ganglia/
- http://boston-cluster.lti.cs.cmu.edu/ganglia/
- http://tir.lti.cs.cmu.edu/ganglia/
- https://workhorse.lti.cmu.edu/ganglia [bad cert]

## See Also

- [Ganglia Official Website](http://ganglia.info/): Official website of Ganglia with documentation, downloads, and community resources.
