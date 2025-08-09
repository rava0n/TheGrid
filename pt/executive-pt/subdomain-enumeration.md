# Subdomain Enumeration

## Utils Tools

* subfinder
* crt.sh

### OWASP Amass

The **OWASP Amass Project** performs network mapping of attack surfaces and external asset discovery using open source information gathering and active reconnaissance techniques.

{% embed url="https://github.com/owasp-amass/amass" %}

<pre class="language-bash" data-overflow="wrap"><code class="lang-bash">amass enum -passive -dir /tmp/amass_output/ -d example.com -o dir/example.com
<strong>
</strong><strong>amass enum -passive -d example.com -o $OUTPUT_FILE
</strong>cat $OUTPUT_FILE | sed 's/\x1b\[[0-9;]*m//g' | grep -oP '(?&#x3C;=\s)[a-zA-Z0-9.-]*\.(com)' > cleaned_subdomains.txt # clean output file
</code></pre>

### sublist3r

```bash
sublist3r -d example.com
```

### Recon-ng

```bash
# Run program
recon-rg

# install and load module
marketplace install hackertarget
modules load hackertarget

# get info of module
info
# set the domain 
options set SOURCE example.com
run

# show findings
show hosts
```



### crt.sh

{% embed url="https://crt.sh/" %}

### Shodan

{% embed url="https://www.shodan.io/" %}

```bash
shodan domain example.com
```



### Google Dorks

The command bellow will find through search engine all subdomain except the `www.domain.com`

```
site:*.domain.com -site:www.domain.com
```



## Certificate Transparency

Certificate Transparency (CT) is an Internet security standard and open-source framework for monitoring and auditing digital certificates. It creates a system of public logs to record all certificates issued by publicly trusted CAs, allowing efficient identification of mistakenly or maliciously issued certificates.

Some CT Logs search engines:

* [https://crt.sh/](https://crt.sh/)
* [https://censys.io/](https://censys.io/)

## Brute Force Enumeration

### SubDomain Brute Force with Amass

```bash
amass enum -brute -w $SUBDOMAIN_FILE.txt -d example.com -o results.txt
```

### SubDomain BruteForce with GoBuster

```bash
gobuster dns -t 30 -w $SUBDOMAIN_FILE.txt -d example.com
```



## DNS

### Zone Transfer

DNS zone transfer is one mechanism to replicate DNS databases across DNS servers.

#### **dnsrecon**

```bash
dnsrecon -d target.com
```

{% embed url="https://dnsdumpster.com/" %}

#### dig

```bash
dig AXFR @$IP_NAME_SERVER example.com
```



## Virtual Host (vhost) Enumeration

In order to choose what website to show for what domain, many use what is called "virtual hosting". Virtual hosting can be based on a name, an IP, or a port ([read more](https://en.wikipedia.org/wiki/Virtual_hosting#Name-based)).

* HTTP: the use of the `Host` request header. The client uses the `directive to connect to the domain name of the server. Optionally, it can use the` directive to specify a TCP port number on which the server is listening.
* HTTPS: the use of the Server Name Indication (SNI) extension with TLS. The client indicates the hostname it wants to connect to at the start of the handshake process.

{% code title="WFuzz" overflow="wrap" %}
```bash
wfuzz -H "Host: FUZZ.something.com" --hc 404,403 -H "User-Agent: PENTEST" -c -z file,"/path/to/wordlist.txt" $URL

# Hide response with specific
# --hc/hl/hw/hh : code/lines/words/chars

# Show response with specific
# --sc/sl/sw/sh : code/lines/words/chars
```
{% endcode %}

{% code title="Gobuster" %}
```bash
gobuster vhost --useragent "PENTEST" --wordlist "/path/to/wordlist.txt" --url $URL
```
{% endcode %}

{% code title="fuff" overflow="wrap" %}
```bash
ffuf -H "Host: FUZZ.$DOMAIN" -H "User-Agent: PENTEST" -c -w "/path/to/wordlist.txt" -u $URL -fs $SIZE_OF_REQUEST_TO_EXCLUDE
```
{% endcode %}



Wordlists:

```
/usr/share/dnsrecon/dnsrecon/data/subdomains-top1mil.txt
/usr/share/dnsrecon/dnsrecon/data/subdomains-top1mil-5000.txt
/usr/share/dnsrecon/dnsrecon/data/subdomains-top1mil-20000.txt
/usr/share/metasploit-framework/data/wordlists/lync_subdomains.txt
/usr/share/metasploit-framework/modules/auxiliary/gather/searchengine_subdomains_collector.rb
/usr/share/amass/wordlists/bitquark_subdomains_top100K.txt
/usr/share/amass/wordlists/subdomains-top1mil-5000.txt
/usr/share/amass/wordlists/subdomains-top1mil-110000.txt
/usr/share/amass/wordlists/subdomains.lst
/usr/share/amass/wordlists/subdomains-top1mil-20000.txt
/usr/share/spiderfoot/spiderfoot/dicts/subdomains.txt
/usr/share/spiderfoot/spiderfoot/dicts/subdomains-10000.txt
```















