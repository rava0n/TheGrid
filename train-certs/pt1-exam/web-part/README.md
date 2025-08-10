# Web Part

## Summary

**TryBankMe** has launched a web application that will serve as its customer-facing portal for digital banking services. Through this platform, customers can sign up for accounts, apply for loans and cards, and manage financial transactions.

Given the sensitive nature of financial applications, this component of the assessment focuses on identifying security vulnerabilities that could compromise user data, application integrity, or transactional accuracy. The web application is considered a cornerstone of the TryBankMe offering, and its security must be validated ahead of the fast-approaching public release.

Your task is to perform a penetration test of the web application for a wide range of web-based vulnerabilities, confirm their impact through exploitation, and report them in a structured, professional format.



## In-Scope

* Assessment target: http://10.200.150.100/
* The application makes use of a supporting API that is in-scope as well.

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

**Note:** Only vulnerabilities that produce flags will be eligible for scoring. Only the first instance of a specific vulnerability type will award points. If you identify a vulnerability but are unable to fully exploit it to receive the flag value, you can submit the vulnerability without the flag for partial credit.

As an example:

* You managed to find an SQL injection vulnerability, exploit and retrieve the flag. - You get full points for this submission.
* The second vulnerability you find is XSS, but are unable to retrieve the flag. You get partial credit.
* If your third submission is another SQL injection vulnerability, even if it is in a different part of the application, you will receive 0 points for it.



## Scoring

This section of the assessment has a total of 400 points distributed as follows:

* 20 points reserved for the report summary
* 380 points divided across the 4 vulnerabilities

Due to time constraints with going live, TryBankMe wants to focus on very specific vulnerabilities. As such, not all vulnerabilities should be reported on or will award you points in the exam. Furthermore, multiple findings of the same vulnerability will only award you points once.

The following vulnerabilities should be focused on and are worth 95 points each:

* Cross Site Scripting (XSS)
* Cross Site Request Forgery (CSRF)
* SQL Injection
* Command Injection
* Unrestricted File Upload
* Arbitrary File Read
* Path Traversal
* Server-Side Request Forgery
* Server-Side Template Injection
* XML External Entity Injection (XXE)
* Mass Assignment
* Insecure Authentication Mechanism
* Broken Access Control
* Broken Business Logic
* Race Condition
* Sensitive Information Disclosure
* Open Redirect

## Flags

TryBankMe values both identification and exploitation of vulnerabilities. In order to help you understand if you have found an impactful vulnerability, TryBankMe has embedded flags in the application. While you may consider some of your findings vulnerabilities, TryBankMe will only consider those that produce a flag for points for the exam.

Flags will automatically appear in the server response if you successfully exploit a vulnerability. In some cases, a vulnerability may be partial, delivering one half of the flag. You can find the vulnerability pair by matching the middle value of the UUID to make one complete flag value. As an example:

* Partial flag 1 - THM{ece436bb-b8ab-4b63
* Partial flag 2 - 4b63-ab71-5f663add5b5a}

4b63 shows the two partial flags are a pair to create the complete flag value, which will be `THM{ece436bb-b8ab-4b63-ab71-5f663add5b5a}`

In the event that you have identified a vulnerability but are unable to gather the flag, you can still submit the vulnerability to receive partial points.

## Client-Side Vulnerabilities

For client-side vulnerabilities, such as Cross-Site Scripting, TryBankMe has a security engineer on call that can verify your finding. In order to verify your finding, please provide the username and password of the account that your client-side attack targets. Your client-side attack should add a new cookie with the name `XSS` and the value `XSS` to the target's browser session. Once ready, you can provide these credentials to the TryBankMe security engineer by making the following cURL request:

`curl -H 'Content-Type: application/json' -X POST -d '{ "username" : "target_username", "password" : "target_password" }' http://10.200.150.100:8080/api/v1.0/xss`

If the security engineer verifies your finding, the flag value will be returned.

**Note:** Make sure that your target account only has one item. For example, if you find XSS in the card feature, your target should only have one card to avoid the security engineer from using the wrong account and therefore not verifying your finding. You may have to create a new target user to ensure only one time.

