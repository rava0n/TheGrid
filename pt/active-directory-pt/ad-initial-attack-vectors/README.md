# AD Initial Attack Vectors

A simple methodology could be this:

* Begin day with **mitm6** or **Responder**
* **Run scans** to generic traffic
* If scans are taking too long, **look for websites** in scope (msfconsole **http\_version**)
* Look for **default credentials** on web logins
  * Printers
  * Jenkins
  * Etc
* **Think outside the box**
