# SSH

# Request an Account
- **[Account Requests](https://docs.google.com/forms/d/e/1FAIpQLSccfWvXdBltL8oxPYEZPGD-IWTnXXPqQS2bwcHr72wpRi1l6A/viewform):** Use this form to request an account or for requesting changes to an existing account.

# SSH Login

To log in to a cluster, use the following command, replacing `<username>` with your username and `<mycluster>` with the fully qualified domain name (FQDN) of your cluster:
```
 ssh <username>@<mycluster>
```

## Passwordless Login

If you want to bypass password authentication, you can use SSH key authentication. SSH keys allow you to connect to a server or multiple servers without entering your password each time.

To generate an SSH key pair, follow these steps:

1. On your local machine, generate an SSH key:

```
 ssh-keygen -t ed25519 -C "[$(whoami)@$(uname -n)-$(date -I)]"
```

1. Automatically add your public key to the server by running the following command (use your ANDREW password):

```
 ssh-copy-id -i ${HOME}/.ssh/id_ed25519.pub <username>@<mycluster>.
```

  # for windows, first navigate to your .ssh folder, then run:
```
 type .\id_ed25519.pub | ssh <username>@<mycluster> "cat >> .ssh/authorized_keys"
```

1. You can also configure your `~/.ssh/config` file to avoid typing the full cluster address every time. Add the following lines to the file:

```
 Host <mycluster>
 HostName <mycluster's hostname>
 User <username>
 IdentityFile ${HOME}/.ssh/id_ed25519
```

1. Then, you can log in with the simple command `ssh *mycluster*`.

## Connecting to a Compute Node

You can also ssh directly to a compute node where you have a job already running by doing:

```
ssh -J babel babel-X-XX
```

More on [Port Forwarding](Port%20Forwarding.md)

## Copying Data to Compute Nodes

To copy data to compute nodes when the data directories are not directly accessible from the login nodes, you can utilize the login node as a JumpHost, allowing direct connections to the compute nodes. Follow these steps to configure SSH and establish the connection:

1. Launch an interactive job on a compute node.
1. Determine the IP address of the compute node by executing the `ip a` command on the compute node itself.
1. Update your SSH configuration file located at `~/.ssh/config` with the following entries:

```
 Host <mycluster-short-name>
   HostName <mycluster-fqdn>
   User <username>
   IdentityFile <identityfile>
```

```
 Host <mycluster>-compute-node
   HostName <node ip addr>
   User <username>
   ProxyJump <mycluster>
```

Replace the relevant items in ` < >` with your actual username, cluster name and the path to your SSH private key file.
For example, to connect to a node in the Babel cluster:

```
Host babel
  HostName babel.lti.cs.cmu.edu
  User <username>
  IdentityFile /path/to/identityfile
```

```
Host babel-compute-node
  HostName <node ip addr>
  User <username>
  ProxyJump babel
```

The updated configuration allows you to establish a connection to the compute node through the login node, acting as a JumpHost.

Refer to your cluster's dedicated page in the [ Cluster's](%3ACategory%3AClusters.md) section for more detailed information.

## Why is there an SSH key pair in the home directory upon first login?

When a user runs a job on two nodes, their script might need to SSH from node-1 to node-2. If the SSH connection fails or hangs, it is often because the fingerprint of the target node is not in the `~/.ssh/known_hosts` file. To prevent this issue, we automatically create SSH keys and add the necessary fingerprints to `~/.ssh/authorized_keys` when the user's home directory is created.

This process also eliminates password prompts when connecting to a node where you have a job running.

For more information on generating these keys, see: [Connecting to the Cluster - Passwordless Login](SSH.md#passwordless_login)

# Troubleshooting

## Windows
### SSH Connection Errors

When attempting to connect to the HPC cluster using Visual Studio Code and SSH, Windows users may encounter the following error:

```
Corrupted MAC on input.
ssh_dispatch_run_fatal: Connection to 128.2.205.73 port 22: message authentication code incorrect
```

#### Solution

To resolve this issue, add the following configuration to your local SSH config file (~/.ssh/config):

Host babel babel-login
```
   HostName babel.lti.cs.cmu.edu
   Ciphers aes128-ctr,aes192-ctr,aes256-ctr
   MACs hmac-sha2-256,hmac-sha2-512,hmac-sha1
   User <USERNAME>
   ForwardAgent yes
```

Replace <USERNAME> with your actual username.

If you are using an SSH key, add the following line, replacing the placeholder with your private SSH key's file path:

```
   IdentityFile C:/Users/<USERNAME>/.ssh/<private_ssh_key_name>
```
