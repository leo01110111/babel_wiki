# HPC Terminology

[Category:General](Category%3AGeneral.md)
[Category:HPC](Category%3AHPC.md)

This article provides an overview of key terminology and concepts related to High-Performance Computing (HPC). It aims to familiarize users with essential terms and their meanings in the context of HPC. By understanding these terms, users can gain a solid foundation for exploring and engaging in the field of high-performance computing.

# Cluster Architecture

The cluster is comprised of several key components:

- `Login Node`: The login node is used for logging into the cluster, launching jobs, and connecting to compute nodes.
- `Head Node`: The control node serves as the primary management and coordination point for the cluster, and is responsible for several key functions:
    - `Ansible Control Node`: The control node is the primary Ansible node, responsible for managing and automating tasks across the entire system.
    - `SLURM Controller`: The control node manages the SLURM installation and configuration, and is responsible for scheduling and managing jobs on the compute nodes.
    - `SLURM Database`: The control node may also serve as the primary database node, storing and managing data related to the cluster’s configuration, job scheduling, and system performance.
- `Compute Nodes`: The compute nodes provide CPU and GPU resources, local scratch space, and network-mounted storage for running compute-intensive tasks.
- `NAS`: The NAS provides network-attached storage for the cluster, allowing users to store and access data from anywhere on the network.

## Node Types
Please be aware that not all HPC clusters utilize login nodes. The following section describes node types commonly found in HPC clusters.

### Login Nodes
Login nodes serve as the entry point for users to access the HPC cluster. Typically, only your home directory will be mounted on the login nodes. It is important to note that other storage data, such as network mounts for `/data/` and `/compute/`, may be available exclusively on the compute nodes.

Not all clusters have this type of node. For more detailed information about the storage and accessibility of specific data directories in your cluster, please refer to your cluster's dedicated page in the [ Cluster's](%3ACategory%3AClusters.md) section.

### Compute Nodes

The cluster includes dedicated GPU nodes that offer accelerated computing capabilities. These GPU nodes are equipped with high-performance GPUs, providing significant computational power for GPU-intensive workloads.

#### GPU Types

The GPU nodes feature a range of GPU types, ensuring compatibility with diverse computational needs. The specific GPU models available on the cluster may vary over time as the cluster hardware is upgraded.

#### RAM

The GPU nodes in the cluster are equipped with generous amounts of RAM to support memory-intensive applications.

#### CPU

In addition to GPUs, the GPU nodes in the cluster also incorporate powerful CPUs to complement the computational capabilities. The CPUs provide the necessary processing power to handle non-GPU tasks and assist in managing parallel computations effectively.

## Filesystem Layout
For detailed information about the storage and accessibility of specific data directories in your cluster, please refer to your cluster's dedicated page in the [ Cluster's](%3ACategory%3AClusters.md) section.

- Each user is provisioned with a home directory:
    - `/home/<username>`

### AutoFS notes:

AutoFS directories are not always mounted as AutoFS is an “on-demand” filesystem. You may need to stat the full path to the files you are looking for. For example, sometimes the output of `ls /compute/` might seem empty. However, if you `ls /compute/<node_name>` the contents of the /scratch dirs from that node will be revealed.

### Data Directories

**MORE INFO NEEDED**

### Local Scratch Partition

When you are frequently accessing large files, you should first move them to the `/scratch` directory of the local machines your job is running on. Read/Write is much faster locally than it is over the network.

The `/scratch` dir of each node is exported via NFS to other nodes on the cluster. The local disk for the node `<node_name>` can be accessed through at `/compute/<node_name>` on other nodes. This will allow for faster access and reduce pressure on the NAS. *(highly recommended for large or frequently accessed files)*.

**Notes**:

- If you `ls /compute/`, it might seem empty. However if you add the compute node's hostname the path autofs will mount the remote directory and you will be able to access it's `scratch` partition. For example:

```
   [dvosler@hpc-1-27 ~]# ls -la /compute/
   drwxr-xr-x   2 root root    0 Jun 23 15:02 .
   dr-xr-xr-x. 21 root root 4096 Jun 23 15:02 ..
```

```
   [dvosler@hpc-1-27 ~]# ls -la /compute/hpc-0-19
   drwxrwxrwt 4 root     root      4096 May 29 10:25 .
   drwxr-xr-x 3 root     root         0 Jun 28 16:17 ..
   drwxrwxr-x 3 dvosler  dvosler   4096 May 11 18:41 things
   -rw-rw-r-- 3 dvosler  dvosler    420 May 11 18:41 and
   -rw-rw-r-- 3 lemmy    hpcusers     1 May 11 18:41 stuff
```

- Compute nodes and scratch should only hold temporary files, in the sense that you should clean up after yourself. If scratch is 85% full, data older than 28 days will be deleted automatically.
