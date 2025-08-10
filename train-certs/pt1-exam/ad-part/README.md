# AD Part

## Summary

TryBankMe's internal infrastructure includes a newly built Active Directory (AD) domain that will eventually support internal operations, identity management, and service authentication across its banking systems. As the domain is still under development, this assessment serves as an early-stage configuration review and compromise simulation.

Your goal is to assess whether misconfigurations, insecure permissions, or poor implementation choices exist that could allow compromise of key AD assets. Specifically, you will test access to both a standard AD-connected server and the domain controller.

This early review will help the TryBankMe team address weaknesses before expanding production infrastructure.

## In-Scope

Assessment Targets:

* 10.200.150.10 - AD Domain Controller
* 10.200.150.20 - Standard domain-joined server/workstation



## Out-of-scope

* Any systems or domains not explicitly listed above
* Backend infrastructure (unless accessible via the web application)
* Phishing or social engineering attacks
* As this is an active client network, any denial-of-service attacks or aggressive attacks/scans aimed at causing service disruptions
* The VPN server: 10.200.150.250



## Deliverables

You must submit a professional penetration testing report that includes the following elements:

* Report Summary: Executive-level overview and key findings
* Per host a writeup containing the following detail:
  * Flag value
  * Step-by-step description of attack path followed to compromise the host
  * Remediation actions to resolve the vulnerabilities exploited in the attack path

No risk ratings or vulnerability-level reporting is required for this section as the focus is on misconfiguration identification and exploit paths.

**Note:** Only vulnerabilities that produce flags will be eligible for scoring. Each host has a flag to be found.

In Active Directory environments:

* Workstations (WRK): Flags can be found at `C:\flag.txt`
* Domain Controllers (DC): Flags can be found at `C:\flag.txt`

Make sure to collect all flags to maximize your score.



## Scoring

This section of the assessment has a total of 240 points distributed as follows:

* 20 points reserved for the report summary
* 220 points divided across the 2 host compromises

An equal amount of points are awarded for both hosts in this pentest.

\
