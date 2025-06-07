# Port Forwarding

## SSH Port Forwarding

I have a SSH access to target machine, and i want to host a target's port to a local port.

```bash
ssh -L local_port:destination_server_ip:remote_port ssh_server_hostname@target_ip
```

For example:

The target machine has a port 3000 hosted in local. I want to see that in my machine on port 8080.

```bash
ssh -L 8080:127.0.0.1:3000 user@10.10.11.68
```
