# Web

## Mind map

### Web App Functionalities

**Explore all functionalities** of the web application and list them to your note, then you will have a list and all vulnerabilities to test for each function.



### OWASP WSTG

Follow this guide.

{% embed url="https://owasp.org/www-project-web-security-testing-guide/stable/4-Web_Application_Security_Testing/" %}



### Special XSS

XSS vulnerability have **special method to get a flag** so “**READ THE RULE OF ENGAGEMENT**” again.



### Business Logic Flaw

This is banking web app, Business Logic Flaw can be a valid way to get your flag as well so if its dealing with number then at least try with negative number.

<details>

<summary>Scenario</summary>

**✅ What it is:**

The application allows users to **enter negative values** for monetary transactions, like `-100`, and it processes them as legitimate.

**🧪 How to test:**

* Go to a **fund transfer** or **deposit** feature.
*   Intercept the request with **Burp Suite**, modify the amount to a negative value:

    ```json
    {
      "amount": -100,
      "recipient": "John123"
    }
    ```
* Send the request.
* Observe if:
  * The balance increases instead of decreasing.
  * The negative transfer is accepted and processed.

**⚠️ Risk:**

Attackers can **credit money to their account** or **debit others improperly**.

</details>

### Use Burpsuite always

Utilize `Burpsuite` to the fullest, it helps you so much on this section when finding for majority of the vulnerabilities that could be hidden on this web app.



### Authorize burp extention

Utilize [Authorize](https://medium.com/@Kuber19/burp-extension-1-autorize-93584a179b85) burp extension to find for broken access control or IDOR, this one recommended by my friend and it changed my life.



### How get the flags

There is **no “flag.txt”** to capture, if you exploited the “valid” vulnerability they designed for and in the “valid” way then the flag will be displayed right away.



### SQLMap

Suspect this function is communicating with database? capture it and run sqlmap in the background while you are testing for other vulnerability and come back to it from time to time, you might find something interesting.



### Make user to admin

Dealing with user? can we make it admin with mass assignment?

Probably we have to upgrade the settings for a user to admin. Or create a new user with the Administrator field.



### Bypassing techniques&#x20;

Learn some bypass technique as recommended here.

\


\


## Prep Lab

{% embed url="https://tryhackme.com/room/picklerick" %}

{% embed url="https://tryhackme.com/room/billing" %}

{% embed url="https://tryhackme.com/room/rabbitstore" %}

{% embed url="https://tryhackme.com/room/silverplatter" %}

{% embed url="https://tryhackme.com/room/avenger" %}

{% embed url="https://tryhackme.com/room/owaspjuiceshop" %}
