# Phishing Setups

## Domain Registration

Domain Registars come in many shapes and sizes, some popular ones are:

* [GoDaddy](../../../)
* [NameCheap](https://www.namecheap.com/)
* [Amazon AWS Route 53](https://aws.amazon.com/route53/)
* [Register.com](https://register.com/)



### AWS Route 53 - Domain Reg

How to register a domain:

1. Create an AWS Account
2. Purchase a Domain via Amazon Route 53 (Usually $10-$15)
3. Be sure to enable privacy protection for your domain
4. Go to hosted zones in your Route 53 dashboard for the next steps



## Email Registrar

Email Registrars come in many shapes and sizes, some popular ones are:

* [Mailgun](https://www.mailgun.com/)
* [Amazon SES](https://aws.amazon.com/it/ses/)
* [Sendgrid](https://sendgrid.com/en-us)
* GoDaddy Email
* Google SMTP Server



### Mailgun setup

SMTP configure steps:

1. Add New Domain from Mailgun's dashboard and insert the purchased domain for the campaign.
2. Setup all DNS Records

**Setup the SPF record**

Copy the SPF value and create a new TXT record in AWS Route 53

<figure><img src="../../../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

**Setup DKIM parameter**

Copy the DKIM Hostname and create another TXT record on AWS

<figure><img src="../../../.gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>



**Setup MX Records**

Copy MX's values and create new MX record on AWS

<figure><img src="../../../.gitbook/assets/image (8).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (9).png" alt=""><figcaption></figcaption></figure>



**Setup CNAME records**

Copy the CNAME's value and create a CNAME record on AWS

<figure><img src="../../../.gitbook/assets/image (10).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (11).png" alt=""><figcaption></figcaption></figure>



3. When all records are done, click "Verify DNS setting" on Mailgun Domain Overview
4. Now we can create new email accounts, that they'll send the phishing emails.

<figure><img src="../../../.gitbook/assets/image (12).png" alt=""><figcaption></figcaption></figure>

