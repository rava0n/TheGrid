# Host Discovery



<pre class="language-bash" data-overflow="wrap"><code class="lang-bash"><strong># NETWORK DISCOVERY
</strong># nmap
nmap -sn IP/MASK

# ARP scan
netdiscover -i INTERFACE -r IP/MASK

# arp
arp-scan -I eth1 IP/MASK

## fping
fping -I eth1 -g IP/MASK -a 2>/dev/null

## fping with no "Host Unreachable errors"
fping -I eth1 -g &#x3C;TARGET_IP/NETWORK> -a fping -I eth1 -g &#x3C;TARGET_IP/NETWORK> -a 2>/dev/null
</code></pre>

```bash
# DNS
sudo nano /etc/hosts
```
