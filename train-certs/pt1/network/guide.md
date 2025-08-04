# Guide

## Rule of Engagement

Read very well the RoE for this section



## Breach phase

Scan all ports for both TCP and UDP port first and rescan gain to cross check in case your first scan miss something.

Get ALL version of the services with:

* nmap
* telnet IP PORT (Banner grabbing)

For the inizial access part we can enumerate very well, and probably the possible entry are in there:

* CVEs: Find out all software version to search a public exploit PoC
* misconfiguration: check the hidden file/dir, secret data disclousure
* default creds: if we find a login page, just try with default creds



## PrivEsc phase

* First of all, run these commands:
  * `whoami /all` on Windows&#x20;
  * &#x20;`sudo -l` on Linux
* Run WinPeas or LinPeas and look for any important data from the output.
  * interesting stuffs still there such as **writable directory** or **installed application**



{% hint style="danger" %}
If you stuck!

create the note again to check what you did and what to do next to keep your sanity in check.
{% endhint %}
