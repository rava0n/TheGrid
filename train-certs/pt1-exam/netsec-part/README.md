# NetSec Part

## Summary

As part of the TryBankMe rollout, internal systems play a critical role in supporting the operation of the banking platform.

To ensure a robust security posture across its infrastructure, TryHackMe has requested a focused pentest of two key internal systems, one Linux- and one Windows-based.

The objective of this engagement is to simulate internal attacker behaviour, identify how these systems could be compromised from a network-accessible position, and determine whether privilege escalation is possible. The goal is to understand real-world risk and provide actionable remediation advice for each host.



## In-Scope

Assessment Targets:

* 10.200.150.151 - Windows
* 10.200.150.152 - Linux

Testing should be performed from the internal network perspective. Both initial access and privilege escalation are in-scope.

## Out-of-Scope

* Any systems or domains not explicitly listed above
* Backend infrastructure (unless accessible via the web application)
* Phishing or social engineering attacks
* As this is an active client network, any denial-of-service attacks or aggressive attacks/scans aimed at causing service disruptions
* The VPN server: 10.200.150.250



## Deliverables

You must submit a professional penetration testing report that includes the following elements:

* Report Summary: Executive-level overview and key findings
* 4 Vulnerability writeups each containing the following detail:
  * Vulnerability Name
  * Risk Rating (Abstracted CVSS Score)
  * Flag Value
  * Description of vulnerability and how it was identified
  * Remediation actions to resolve the root cause of the vulnerability

**Note:** Only vulnerabilities that produce flags will be eligible for scoring. Each host has an initial access and privilege escalation flag. For Linux the flags can be found in `/user.txt` and `/root/root.txt` for privileged access and for Windows the flags can be found in `C:\user.txt` and `C:\Users\Administrator\root.txt`. Only the first instance of a specific vulnerability type will award points. If you identify a vulnerability but are unable to fully exploit it to receive the flag value, you can submit the vulnerability without the flag for partial credit.

### Breach Reporting

For initial access (breach), the following vulnerabilities can be chosen:

* Outdated Software
* Default Software Configuration
* Misconfigured Service Configuration
* SQL Injection (Custom App)
* Command Injection (Custom App)
* Unrestricted File Upload (Custom App)
* Arbitrary File Read (Custom App)

In the event that a publicly known software product or service is vulnerable, one of the first 3 vulnerability names should be chosen. In the event that a custom TryBankMe application is vulnerable, one of the last 4 vulnerability names should be chosen.



### PrivEsc Reporting

For privilege escalation (privesc), the following vulnerabilities can be chosen:

* Outdated Software
* Insecure File Permissions
* Insecure Path Configuration
* Insecure Crontab/Service/Scheduled Task Configuration
* Insecure Sudo/SUID Configuration
* Insecure Capability/Privilege Configuration
* Insecure Registry Configuration
