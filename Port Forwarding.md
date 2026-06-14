# Port Forwarding

This guide explains how to use SSH port forwarding to expose or access services from an HPC compute node behind our NAT setup. Our NAT server (the HPC head node) is accessible via `login1.babel.cs.cmu.edu`, which acts as our login node with a fully configured SSH daemon. You don’t need admin rights—just your user account and SSH credentials. 

## What You Need
- A running service on your compute node (e.g., a web server on port 8080) for remote forwarding.
- Your SSH credentials (username and either a password or SSH key) for `login1.babel.cs.cmu.edu`.
- A free port number on the login node (see [Choosing a Port](#choosing-a-port)).
- For local or dynamic forwarding, know the external service or proxy needs.

## Why Use Port Forwarding?
Our compute nodes are behind a NAT (the HPC head node), so they don’t have public IPs. SSH port forwarding lets you:
- **Expose** a local service to the web (remote forwarding).
- **Access** external services from your node (local forwarding).
- **Browse** securely via a proxy (dynamic forwarding).

## How It Works
You run SSH commands from your compute node, connecting to `login1.babel.cs.cmu.edu` (the login node). This node doubles as our NAT gateway, forwarding traffic between your node and the outside world. The setup is the same whether you’re exposing a service or pulling one in.

## Remote Port Forwarding (Expose a Service)

### Steps
1. **Check Your Service**: Ensure it’s running on your node, e.g.:

```
curl localhost:8080
```

2. **Choose a Port**: Pick a free port on `login1.babel.cs.cmu.edu` (e.g., 9000–9999).
3. **Run the Command**:

```
ssh -R 9000:localhost:8080 yourusername@login1.babel.cs.cmu.edu
```

4. **Keep It Alive**: Add `-o ServerAliveInterval=60`:

```
ssh -R 9000:localhost:8080 -o ServerAliveInterval=60 yourusername@login1.babel.cs.cmu.edu
```

5. **Test It**: From outside, visit `http://login1.babel.cs.cmu.edu:9000`.

### Example
Expose a web server on port 8080:

```
ssh -R 9500:localhost:8080 yourusername@login1.babel.cs.cmu.edu
```

Anyone can access it at `login1.babel.cs.cmu.edu:9500`.

## Local Port Forwarding (Access an External Service)

### When to Use
Use local forwarding to bring an external service (e.g., a web API) into your compute node, as if it’s running locally.

### Steps
1. Identify the external service (e.g., `api.example.com:80`).
2. Run this from your compute node:

```
ssh -L localhost:8080:api.example.com:80 yourusername@login1.babel.cs.cmu.edu
```

3. Access it on your node at `localhost:8080` (e.g., `curl localhost:8080`).

### Example
Pull a public web server into your node:

```
ssh -L localhost:5000:www.google.com:80 yourusername@login1.babel.cs.cmu.edu
```

Now, `curl localhost:5000` on your node shows Google’s homepage.

## Dynamic Port Forwarding (SOCKS5 Proxy)

### When to Use
Dynamic forwarding turns your SSH connection into a SOCKS5 proxy, letting you browse the web or access multiple services through `login1.babel.cs.cmu.edu`.

### Steps
1. Run this from your compute node:

```
ssh -D 1080 yourusername@login1.babel.cs.cmu.edu
```

2. Configure your web browser to use a SOCKS5 proxy:
   * Host: `localhost`
   * Port: `1080`
   * Type: SOCKS5
```
  (In Firefox: Preferences > Network Settings > Manual Proxy Configuration.)
```
3. Browse normally—traffic goes through the login node.

### Example
Set up a proxy:

```
ssh -D 1080 yourusername@login1.babel.cs.cmu.edu
```

In your browser on the compute node, set the SOCKS5 proxy to `localhost:1080`. Visiting `whatismyip.com` shows `login1.babel.cs.cmu.edu`’s IP.

## Choosing a Port
- For remote forwarding: Use 9000–9999 on `login1.babel.cs.cmu.edu`.
- For local/dynamic: Ports like 8080 or 1080 are on your node, so they’re flexible.
- Coordinate with teammates to avoid conflicts on the login node.

## Security Tips
- Remote forwarding exposes your service to anyone hitting the login node’s port—share carefully.
- Use SSH keys (not passwords) for better security. Ask an admin for setup help.
- Kill the SSH session (`Ctrl+C`) when done.

## Troubleshooting
- **Remote: “Connection refused”**: Check your service (`curl localhost:<port>`) or try another login node port.
- **Local: No response**: Ensure the external service is reachable from `login1.babel.cs.cmu.edu`.
- **Dynamic: Proxy fails**: Confirm your browser’s SOCKS5 settings and that SSH is running.
- SSH drops? Use `-o ServerAliveInterval=60` or `autossh`.

## HPC Note
Our NAT is the HPC head node, but you connect via `login.babel.cs.cmu.edu` (the login node). You don’t interact with the NAT directly—it’s all handled behind the scenes. Just run the SSH commands from your compute node as shown.

## See Also
- [SSH Basics](SSH.md) – More SSH command info.
