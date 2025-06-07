---
description: >-
  Active Directory (AD) penetration testing is a security assessment process
  aumed at evaluating the security posture of an organizzation's Active
  Directory infrastructure.
---

# AD Penetration Testing

## Active Directory Kill Chain&#x20;

<figure><img src="../../.gitbook/assets/image (139).png" alt=""><figcaption></figcaption></figure>

* Initial Compromise
* Host Reconnaissance
* Domain Enumeration
* Local Priv Esc
* Admin Enumeration
* Lateral Movement
* Domain Admin privs
* Cross Trust Attacks
* Domain Presistence
* Exfiltrate

## Breaching AD

The scope of this phase is try to access into Active Directory environment.

**Techniques**:

* **Password Spraying**: Attempt to authenticate using common or leaked passowrds across multiple user accounts.
* **Brute Force Attacks**: Use automated tools to guess passwords for user acounts with weak or default passowrds.
* **Phishing**: Craft convincing emails to trick users into revealing their credentials or executing malicious attachments.
* **Poisoning**: LLMNR/NBT-NS Poisoning

## AD Enumeration

Once we have breached into AD, lets start enumeration all

Techniques:

* **PowerView**: Enumerate AD objects, attributes, and permissions to identify potential security weaknesses.
* **BloodHound**: Visualize and analyze AD permissions, group memberships, and attack paths.
* LDAP Enumeration: Query the AD LDAP service to retrieve detailed information about, users, groups, computers, and OUs.

## Privilege Escalation

Try to get high privilege from normal user

**Techniques**:

* **Kerberosting**: Kerberosting is a Kerberos attack that targets service accounts to obtain their encrypted **service account passowords (SPNs)**, which can be cracked offlice to obtain plaintext passwords.
* **AS-REP Roasting**: is a Kerberos authentication attack that targets user accounts with the "Do not require Kerberos preauthentication" attribute set.

## Lateral Movement



Techniques:

* **Pass-the-Hash**: Use stolen password hashes to authenticate and gain access to other systems without knowing the plaintext passwords.
* **Pass-the-Ticket (PtT)**: is a technique user in Active Directory environments to authenticate to other systems or services using stolen Kerberos ticket-granting tickets (TGTs) or service tickets without knowing the user's plaintext password.

## Persistence

Stay in!

Techniques:

* Silver Ticket: a silver ticket attack is a technique used in AD environments to impersonate any service or computer account by forging Kerberos service tickets without the need to obtain the account's plaintext passoword.
* Golden Ticket: Golden Ticket attacks involve forging Kerberos tickets for arbitraty users, allowing attackers to impersonate any user and access any resources within the AD environment.



## AD Resources

### AD PT mindmap

{% embed url="https://orange-cyberdefense.github.io/ocd-mindmaps/" %}

### AD Exploitation Cheat Sheets

{% embed url="https://github.com/S1ckB0y1337/Active-Directory-Exploitation-Cheat-Sheet" %}



