# TryBankMe



## Account credentials

Parameter for User creation from Register page

<figure><img src="../../../.gitbook/assets/image (366).png" alt=""><figcaption></figcaption></figure>

Second Account

<figure><img src="../../../.gitbook/assets/image (383).png" alt=""><figcaption></figcaption></figure>



## Enumeration

### /register

<figure><img src="../../../.gitbook/assets/image (370).png" alt=""><figcaption></figcaption></figure>

Request which creates an account

<figure><img src="../../../.gitbook/assets/image (371).png" alt=""><figcaption></figcaption></figure>



### /login

<figure><img src="../../../.gitbook/assets/image (367).png" alt=""><figcaption></figcaption></figure>

Request to make login:

<figure><img src="../../../.gitbook/assets/image (368).png" alt=""><figcaption></figcaption></figure>

JWT encoded:

```json
{
  "username": "mariorossi99",
  "role": 0,
  "exp": 1754810073
}
```

Get information from API using `Authorization` header

<figure><img src="../../../.gitbook/assets/image (369).png" alt=""><figcaption></figcaption></figure>



### /accounts/create

this endpoint allows someone to crarte a new financial account.

<figure><img src="../../../.gitbook/assets/image (372).png" alt="" width="534"><figcaption></figcaption></figure>

Request which make the account

<figure><img src="../../../.gitbook/assets/image (373).png" alt=""><figcaption></figcaption></figure>



### /transactions/create

Create a new account to test vuln

Target Account number: `948ae97e-83ac-466e-89ca-0d4e95996a55`&#x20;

<figure><img src="../../../.gitbook/assets/image (374).png" alt="" width="534"><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (377).png" alt=""><figcaption></figcaption></figure>

An attacker could insert a payload in the message field, because it not has sanitization check.

<figure><img src="../../../.gitbook/assets/image (375).png" alt="" width="551"><figcaption></figcaption></figure>

{% hint style="danger" %}
Test for Buisiness Logic Flaw with negative number.
{% endhint %}



### /cards/create

Method to make a new card

<figure><img src="../../../.gitbook/assets/image (376).png" alt=""><figcaption></figcaption></figure>





### /loans/create

Method to request new loan

<figure><img src="../../../.gitbook/assets/image (378).png" alt="" width="552"><figcaption></figcaption></figure>

{% hint style="danger" %}
This request will be review by a bank employee, the Description can use for XSS vulnerability
{% endhint %}

<figure><img src="../../../.gitbook/assets/image (379).png" alt=""><figcaption></figcaption></figure>



### /vault/upload

Method to upload a file in the Vault

<figure><img src="../../../.gitbook/assets/image (380).png" alt="" width="458"><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (381).png" alt=""><figcaption></figcaption></figure>





## Vulnerabilities

### Broken Business Logic

Intercepting a request that should make a transiction to another user, an attacker can change the AMOUNT parameter for the http:10/200/150/100:8080/api/v1.0/transaction POST endpoint to a negative number to receive money instead give them without other user authorization. This is a Broken Business Logic Vulnerability.

<figure><img src="../../../.gitbook/assets/image (382).png" alt=""><figcaption></figcaption></figure>



{% code title="Vuln flag" %}
```
THM{f1919257-f4f0-44c5-b215-e56e4c7c5255}
```
{% endcode %}



### Mass Assessment

Doing the Password change request, we can try to add other parameters of the user such the Role parameter found in the JWT token.

```json
{
  "username": "mariorossi99",
  "role": 0,
  "exp": 1754810073
}
```

An attacker could intercept and change the request obtain a Privilege Escalation

<figure><img src="../../../.gitbook/assets/image (384).png" alt=""><figcaption></figcaption></figure>



{% code title="Vuln flag" %}
```
THM{29c5a246-be0a-4bca-84c8-38cbed31c34c}
```
{% endcode %}



### XSS Stored

In the Loans page(http://10.200.150.100/loans) when someone create a New Loan request, the parameter Description doesn't filter from special character and this could generate a Stored XSS vulnerability.

Craft the payload which set new cookie XSS with value XSS like the Deliverables said.

```
<img src=1 onerror="document.cookie=document.cookie='XSS=XSS;'">
```

An attacker could intercept the web request and insert this payload to the Description field.

<figure><img src="../../../.gitbook/assets/image (385).png" alt=""><figcaption></figcaption></figure>

Once a emploeey visit the request that cookie will add in his browser page.

<figure><img src="../../../.gitbook/assets/image (387).png" alt=""><figcaption></figcaption></figure>



{% code title="Vuln flag" overflow="wrap" %}
```bash
curl -H 'Content-Type: application/json' -X POST -d '{ "username" : "paolobianchi99", "password" : "Password123!" }' http://10.200.150.100:8080/api/v1.0/xss

{"flag":"THM{38af2c0c-0a05-45a1-8fe6-460328cf923d}","message":"XSS Success"}
```
{% endcode %}







