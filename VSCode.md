# VSCode

[Category:Tips](Category%3ATips.md)

**Note:** [VSCodium](https://github.com/VSCodium/vscodium) is an open-source variant of Visual Studio Code that provides the same powerful features and extensibility, but without the telemetry and branding associated with the official version.

# Using VSCode

When using vscode on babel, you can use remote-ssh functionality of VSCode to connect directly to a compute node.
This requires that you first create an active job on the compute node, then use an ssh proxy to ssh through a login node to the compute node.
To set up the SSH configuration for connecting to the compute node, follow these steps:

1. If you have not already, set up [passwordless login](https://wiki.babel.cs.cmu.edu/index.php/SSH?title=SSH_Login#Passwordless_Login) using a public/private key pair. Take a note of the location on your local machine of the ssh private key (e.g. `~/.ssh/id_rsa`) that you are using for passwordless login, we will call it `identity_file` below.

2. Run a job that allocates the resources that you need for vscode development (including a GPU if you need a GPU machine). Here is an example: `srun --cpus-per-task 2 --mem=8000 --wait=0 -t 02:00:00 -n 1 --pty bash --partition=debug`. Take a note of what machine it is running on, we will call this `node_id`.

3. On your local machine, update your SSH configuration file (`~/.ssh/config`) with the following entries. :

```
 Host babel
   HostName login.babel.cs.cmu.edu
   User <andrew_id>
   IdentityFile <identity_file>
   Compression no
   ForwardAgent yes
   ForwardX11 no
```

```
 Host babel-compute-node
   HostName <node_id>
   User <andrew_id>
   IdentityFile <identity_file>
   ProxyJump babel
   StrictHostKeyChecking no
   UserKnownHostsFile /dev/null
   LogLevel ERROR
```

4. After configuring SSH, run VSCode natively on your local machine and use the command palette to type `Remote-SSH: Connect to Host....` Select `babel-compute-node` from the list.

### Alternative: VSCode Tunnel
The tunnel feature of VSCode CLI allows you to avoid setting up proxy jump with SSH Config, since the VSCode server talks directly to vscode.dev. Vscode.dev will then host an instance which can be accessed with a browser (even on your phone).

Pros: Easy to set up. Cons: Occasional performance issues due to network bandwidth. 

#### Prerequisite
Download VSCode server for Linux x64. You can get a copy at `/data/tir/projects/tir6/general/vscode_server`.

#### Steps

1. Launch a job with command `./code tunnel --accept-server-license-terms`. Ensure that you request sufficient memory and cores.
1. Wait for you job to be launched and the stdout should contain a url (starting with `vscode.dev`) and optionally an authentication method.

**Sample stdout**
```text
*^M
* Visual Studio Code Server^M
*^M
* By using the software, you agree to^M
* the Visual Studio Code Server License Terms (https://aka.ms/vscode-server-license) and^M
* the Microsoft Privacy Statement (https://privacy.microsoft.com/en-US/privacystatement).^M
*
[2024-03-05 11:51:03] info Creating tunnel with the name: tir-1-32

Open this link in your browser https://vscode.dev/tunnel/tir-1-32/home/user

To grant access to the server, please log into https://github.com/login/device and use code AB04-0E14
```

## Connecting to a Compute Node

You can also ssh directly to a compute node where you have a job already running by doing:

```
ssh -J babel babel-X-XX
```
