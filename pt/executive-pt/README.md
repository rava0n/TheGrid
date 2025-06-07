# Executive PT

## Network Discovery

Start with host discovery to find all device in LAN.

```bash
# nmap
nmap -sn IP/MASK -oN hosts_disc.txt

# ARP scan
arp-scan -I INTERFACE IP/MASK
netdiscover -i INTERFACE -r IP/MASK

# IMPORTANT!!!!
cat /etc/hosts
```

{% content-ref url="host-discovery.md" %}
[host-discovery.md](host-discovery.md)
{% endcontent-ref %}

***

## Port Scanning

For each ip found in LAN we'll do a compleate scan port that include all TCP ports discover, OS discover, vulnerability default script.&#x20;

```bash
nmap -sC -sV -p- -O IP -oN ip_port_scan.txt 
```

{% content-ref url="port-scanning.md" %}
[port-scanning.md](port-scanning.md)
{% endcontent-ref %}

***

## Enumeration/Exploitation







***

## Post Exploitation



