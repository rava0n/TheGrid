# AD Foundamentals

## AD Authentication

AD supports several authentication protocol that facilitate secure communication and user authentication within an AD Domain environment.\
Tha main protocols are:

* **Kerberos**
* **NTML**

### Kerberos Auth

Kerberos is the primary authentication protocol used by Active Directory for user authentication

#### How It works

* When a user attempts to log in, their client computer requests a **Ticket Granting Ticket (TGT)** from **Key Distribution Center (KDC)**, which is typically a domain controller.
* The domain controller verifies the user's credentials and issues a TGT if authentication is successful
* The THT is then used to obtain Service Tickets for accessing specific network resources.

<figure><img src="../../.gitbook/assets/image (163).png" alt="" width="375"><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (164).png" alt="" width="375"><figcaption></figcaption></figure>

#### Features

* **Mutual authentication**: Both the client and server verify each other's identities.
* **Single Sign-On (SSO)**: Users authenticate once and can access multiple resources without re-entering credentials
* **Ticket-based**: Authentication exchanges rely on encrypted tickets, reducing the risk of credential theft.

### NTLM Auth

NTLM is an older authentication protocol used by Windows systems for backward compatibility.

#### How it works:

* When a user attempts to log in, their client computer sends a hashed version of their password to the server.
* The server compares the hash to the stored hash of the user's password
* If the hashes match, authentication is successful.

<figure><img src="../../.gitbook/assets/image (166).png" alt="" width="563"><figcaption></figcaption></figure>

#### Features:

* **Compatibility**: NTLM is supported by older Windows systems and applications.
* **Simplicity**: NTLM authentication does not require the complexity of Kerberos, making it easier to implement in certain environments.
* **Security**: NTLM has security limitations compared to Kerberos, including susceptibility to pass-the-hash attacks and lack of mutual authentication.

## AD Primary Group ID

The primary group ID defines the default group membership for a user account.

_**Domain Admins** = primaryGroupToken **512**_\
&#xNAN;_**Domain Users** = primaryGroupToken **513**_\
&#xNAN;_**Guest** = primaryGroupToken **514**_\
&#xNAN;_**Domain Computers** = primaryGroupToken **515**_\
&#xNAN;_**Domain Controllers** = primaryGroupToken **516**_\
&#xNAN;_**Schema Admins** = primaryGroupToken **518**_\
&#xNAN;_**Enterprise Admins** = primaryGroupToken **519**_\
&#xNAN;_**Protected Users =** primaryGroupToken **525**_\








