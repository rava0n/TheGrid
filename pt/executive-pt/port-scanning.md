# Port Scanning

## Nmap Port Scanning

{% code title="Main scan" %}
```bash
# TCP
nmap -T4 -A -p- --min-rate 1000 --open -oN port_scan.txt IP

# UDP Top 1000
nmap -A -sU --script=*enum* -oN udp_top_1000.txt IP 
```
{% endcode %}

{% code title="" %}
```bash
nmap -sN IP
# -sN: Null scan 


```
{% endcode %}

```bash
## Generic Scan
nmap -Pn -F -sV -sC -p- IP -O -oA output.txt
# -Pn: avoid ICMP messages
# -F: Fast Scan
# -sV: Sevice name/version
# -sC: default nmap script
# -p-: All ports
# -O: O.S. detection scan
# -oA: all output

## UDP and specific port scan
nmap -sU -p 80,22 IP
# -sU: UDP port scan
# -p: Specific port

## Output scan
nmap -oN outputfile.txt IP 
# -oN: text output
nmap -oX outputfile.xml IP 
# -oX xml output

## Output to all formats
nmap -oA outputfile IP
# -oA: all output 

## Scan Mode:
nmap -T2 IP
# -T0: Paranoid
# -T1: Sneaky
# -T2: Polite
# -T3: Normal
# -T4: Agressive
# -T5: Insane
```

## Powershell Port Scanning

<pre class="language-powershell" data-overflow="wrap"><code class="lang-powershell"># scanning open TCP ports from a PowerShell query
<strong>C:\Users> 1..1024 | % {echo ((new-object Net.Sockets.TcpClient).Connect("10.0.0.100",$_)) "Port $_ is open!"} 2>$null
</strong>
# scan IP addresses 10.10.10.1-5 and some specific common TCP ports
C:\Users> 1..20 | % { $a = $_; write-host "------"; write-host "10.0.0.$a"; 22,53,80,445 | % {echo ((new-object Net.Sockets.TcpClient).Connect("10.1.1.$a",$_)) "Port $_ is open!"} 2>$null} 
</code></pre>
