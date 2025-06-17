# CrackMapExec

CrackMapExec (CME) is a powersful open-source pentration testing tool.

## Use Cases

* **Network Enumeration and Reconnaissance**: CME can scan networks to identify hosts, enumerate shares, and gather valuable information.
* **Credential Testing and Brute Forcing**: CME can test credentials against network resources, checking for weak or reused passwords. This can help penetration testers identify potential vulnerabilities.
* **Lateral Movement**: Using various techniques, CME can move laterally across a network, exploting valid credentials, hashes, or Kerberos ticket
* **Privilege Escalation**: CME can attempt to escalate privleges on remote systems, providing deeper access to resourcfes.
* **Remote Command Execution**: CME can execute commands on remote systems over SMB, WMI, or other protocols, allowing for flexible remote management.

## CME DB

This db allows us to obtain all the information obtained in previous attacks with crackmapexec.

```bash
cmedb
> help # to get the commands list
```

## CME Modules

To see all CME module execute this command:

{% code title="List of Modules" %}
```bash
crackmapexec <PROTOCOL> -L
```
{% endcode %}

{% code title="Use a Module" %}
```bash
crackmapexec smb <IP> -u USER -p PASSWORD -M <MODULE_NAME> --options
crackmapexec smb <IP> -u USER -p PASSWORD -M <MODULE_NAME> -o MODULE_OPTIONS=VALUE
```
{% endcode %}





