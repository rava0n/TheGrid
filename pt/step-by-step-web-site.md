# Step-by-Step Web Site



## Dir/File fuzzing

{% content-ref url="executive-pt/enumeration-exploitation/web/" %}
[web](executive-pt/enumeration-exploitation/web/)
{% endcontent-ref %}



## Vulnerable Web Service

Try to find out the **Version of the web service**.

Once we got it, **search some public exploit** or **web vulnerability**.

{% content-ref url="executive-pt/enumeration-exploitation/web/" %}
[web](executive-pt/enumeration-exploitation/web/)
{% endcontent-ref %}



## Request monitoring

First Things First we can open Burp Suite, active the Burp Suite proxy in the Web Browser and navigate normally into a Target website.

Then, enable all file traffic monitoring on the `Proxy` > `HTTP History` like this:

<figure><img src="../.gitbook/assets/image (282).png" alt=""><figcaption></figcaption></figure>

Now we can see if there are some juicy request for LFI or IDOR vulnerability.

{% content-ref url="executive-pt/enumeration-exploitation/web/common-web-vuln/idor.md" %}
[idor.md](executive-pt/enumeration-exploitation/web/common-web-vuln/idor.md)
{% endcontent-ref %}

{% content-ref url="executive-pt/enumeration-exploitation/web/common-web-vuln/file-inclusion-lfi-rfi-path-trav.md" %}
[file-inclusion-lfi-rfi-path-trav.md](executive-pt/enumeration-exploitation/web/common-web-vuln/file-inclusion-lfi-rfi-path-trav.md)
{% endcontent-ref %}



## Input vulnerability

Check all input inside the web app, possible vulnerability:

* SQL Injection

{% content-ref url="executive-pt/enumeration-exploitation/web/common-web-vuln/sql-injection.md" %}
[sql-injection.md](executive-pt/enumeration-exploitation/web/common-web-vuln/sql-injection.md)
{% endcontent-ref %}

* XSS

{% content-ref url="executive-pt/enumeration-exploitation/web/common-web-vuln/xss.md" %}
[xss.md](executive-pt/enumeration-exploitation/web/common-web-vuln/xss.md)
{% endcontent-ref %}

* Command Injection

{% content-ref url="executive-pt/enumeration-exploitation/web/common-web-vuln/command-injection.md" %}
[command-injection.md](executive-pt/enumeration-exploitation/web/common-web-vuln/command-injection.md)
{% endcontent-ref %}

* SSTI

{% content-ref url="executive-pt/enumeration-exploitation/web/common-web-vuln/ssti.md" %}
[ssti.md](executive-pt/enumeration-exploitation/web/common-web-vuln/ssti.md)
{% endcontent-ref %}



## Web Enumeration

{% content-ref url="executive-pt/enumeration-exploitation/web/" %}
[web](executive-pt/enumeration-exploitation/web/)
{% endcontent-ref %}



## Vuln Scan

{% content-ref url="executive-pt/enumeration-exploitation/web/vuln-scan.md" %}
[vuln-scan.md](executive-pt/enumeration-exploitation/web/vuln-scan.md)
{% endcontent-ref %}



