# Phishing Infrastructure



## Modern Security Architecture

<details>

<summary>Inbound</summary>

<figure><img src="../../../.gitbook/assets/image (19).png" alt=""><figcaption></figcaption></figure>



</details>

<details>

<summary>Outbound</summary>

<figure><img src="../../../.gitbook/assets/image (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>



</details>

<details>

<summary>Post Compromise</summary>

<figure><img src="../../../.gitbook/assets/image (2) (1) (1).png" alt=""><figcaption></figcaption></figure>



</details>

<details>

<summary>Post Compromise (MFA Bypass)</summary>

<figure><img src="../../../.gitbook/assets/image (3) (1) (1).png" alt=""><figcaption></figcaption></figure>



</details>



## Phishing Compaign with GoPhish in a nutshell

* Create a domain
* Create an email account and setup domain records
* Create an EC2 instance
* Download/install go, Download gophish
* Setup gophish
* Point a DNS A record to your public IP Address
* Setup TLS certificates for gophish&#x20;
* Open port 3333 and 3380 to YOUR IP Address
* Setup sender email, groups to target and the Campaign on gophish
* Open port 443 to THE INTERNET
* Test the Campaign&#x20;
* Send the Campaign
* Profit



