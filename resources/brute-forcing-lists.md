# Brute Forcing Lists

## Web-Content

#### Online resources

{% embed url="https://github.com/danielmiessler/SecLists/tree/master/Discovery/Web-Content" %}

{% code title="Starter Kit Web List" %}
```bash
sudo wget https://raw.githubusercontent.com/danielmiessler/SecLists/master/Discovery/Web-Content/raft-large-files.txt 
sudo wget https://raw.githubusercontent.com/danielmiessler/SecLists/master/Discovery/Web-Content/raft-large-directories.txt 
```
{% endcode %}

#### System resources

**For Directory:**

```
/usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-big.txt
/usr/share/wordlists/dirb/common.txt
/usr/share/wordlists/dirb/big.txt
/usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
```

**For File:**

<pre><code>/usr/share/wordlists/SecLists/Discovery/Web-Content/raft-large-files.txt
<strong>/usr/share/wordlists/SecLists/raft-large-files.txt
</strong>/usr/share/wordlists/dirb/common.txt
/usr/share/wordlists/SecLists/Discovery/Web-Content/versioning_metafiles.txt
</code></pre>

### Subdomain

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

## Usernames

```
/usr/share/metasploit-framework/data/wordlists/common_users.txt
/usr/share/wordlists/metasploit/unix_users.txt
```

```bash
# names list
/usr/share/wordlists/wfuzz/others/names.txt
```

## Passwords

```bash
# kali
/usr/share/metasploit-framework/data/wordlists/unix_passwords.txtù
/usr/share/wordlists/metasploit/root_userpass.txt
/usr/share/nmap/nselib/data/passwords.lst
```

### Password Generator Tools

#### CUPP

```bash
git clone https://github.com/Mebus/cupp

python3 cupp.py -i
```

{% embed url="https://github.com/Mebus/cupp" %}





## Default Credentials



### Default Credentials Cheat Sheet Tool

```bash
# installation with pip
pip3 install defaultcreds-cheat-sheet

# manual installation
git clone https://github.com/ihebski/DefaultCreds-cheat-sheet
pip3 install -r requirements.txt
cp creds /usr/bin/ && chmod +x /usr/bin/creds

# using tool
creds search $SERVICE # ex. tomcat 
```

{% embed url="https://github.com/ihebski/DefaultCreds-cheat-sheet" %}

#### File CSV:

{% embed url="https://github.com/ihebski/DefaultCreds-cheat-sheet/blob/main/DefaultCreds-Cheat-Sheet.csv" %}



