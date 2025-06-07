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

















