# Armitage -Metasploit

{% embed url="https://www.kali.org/tools/armitage/" %}

**Armitage** is a **scriptable red team collaboration tool** for Metasploit that visualizes targets, recommends exploits, and exposes the advanced post- exploitation features in the framework.

## Setup Armitage

```bash
# start postgresql database
service postgresql start

# run armitage
armitage
```

<figure><img src="../../.gitbook/assets/image (98).png" alt=""><figcaption><p>DB connection</p></figcaption></figure>

### Add host

We can get started by adding the IP address/domain name of the target machine; this can be done by clicking on Hosts on the toolbar and on Add Hosts in the sub-menu, as shown in the following screenshot.

<figure><img src="../../.gitbook/assets/image (100).png" alt=""><figcaption></figcaption></figure>

You will then be prompted to add the host address of the target system as shown in the following screenshot.

Enter with the IP or HOSTNAME and click on "Add."

### Scan a Host

We can perform a port scan on the target system by right-clicking on the system and clicking on Scan as shown in the following screenshot.

<figure><img src="../../.gitbook/assets/image (101).png" alt=""><figcaption><p>Scan a host</p></figcaption></figure>

We can also perform an Nmap port scan on the target system by clicking on the hosts menu in the toolbar and the Nmap Scan option in the submenu as shown in the following screenshot.

<figure><img src="../../.gitbook/assets/image (102).png" alt=""><figcaption><p>Quick Scan on a host</p></figcaption></figure>

