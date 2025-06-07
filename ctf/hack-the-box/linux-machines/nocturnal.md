---
hidden: true
---

# Nocturnal

## Machine Info



## Port Scanning

```bash
nmap -T4 -sV -p- --min-rate 1000 --open -oN port_scan.txt 10.10.11.64

Nmap scan report for 10.10.11.64
Host is up (0.039s latency).
Not shown: 64450 closed tcp ports (reset), 1083 filtered tcp ports (no-response)
Some closed ports may be reported as filtered due to --defeat-rst-ratelimit
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.12 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    nginx 1.18.0 (Ubuntu)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```



## Web Enumeration

Searching the IP into browser we failed to connect to a web page, but we noticed that the URL became `nocturnal.htb` . Change the /etc/hosts file to view the page.

```bash
sudo echo "10.10.11.64    nocturnal.htb" >> /etc/hosts
```

We have a web file storing.

<figure><img src="../../../.gitbook/assets/image (57).png" alt=""><figcaption></figcaption></figure>

As soon as we register and login to a website we can upload file to a web storage. Let's try to upload a file with a web shell inside.

As we could imagined the file are filtered and the allow tryped are those:

<figure><img src="../../../.gitbook/assets/image (58).png" alt=""><figcaption></figcaption></figure>

View the source code of the page that allow you to download a stored file, we can look this:

<figure><img src="../../../.gitbook/assets/image (59).png" alt=""><figcaption></figcaption></figure>

We can try to do this request changing the username.

Insert a number user we can look this:\


<figure><img src="../../../.gitbook/assets/image (61).png" alt=""><figcaption></figcaption></figure>

The string "User not found" means that we could try to do a brute force attack to enumerate some users.

<figure><img src="../../../.gitbook/assets/image (62).png" alt=""><figcaption></figcaption></figure>

The "amanda" user exist and she has this file:

<figure><img src="../../../.gitbook/assets/image (51).png" alt=""><figcaption></figcaption></figure>

And this odt file contain this text:

{% code overflow="wrap" %}
```
Dear Amanda,
Nocturnal has set the following temporary password for you: arHkG7HAI68X8s1J. This password has been set for all our services, so it is essential that you change it on your first login to ensure the security of your account and our infrastructure.
The file has been created and provided by Nocturnal's IT team. If you have any questions or need additional assistance during the password change process, please do not hesitate to contact us.
Remember that maintaining the security of your credentials is paramount to protecting your information and that of the company. We appreciate your prompt attention to this matter.

Yours sincerely,
Nocturnal's IT team
```
{% endcode %}

{% hint style="success" %}
Credential:

amanda:arHkG7HAI68X8s1J
{% endhint %}

## Admin panel with creds

Now that we have credential for the user 'amanda', connect in the web portal as she.

<figure><img src="../../../.gitbook/assets/image (52).png" alt=""><figcaption></figcaption></figure>

We can see that there is a new option for the Admin Panel forward.

<figure><img src="../../../.gitbook/assets/image (53).png" alt=""><figcaption></figcaption></figure>

As we can see the Admin panel contain the backup of the site filtered by PHP file.&#x20;







