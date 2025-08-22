# Bypass Mail Defences

## MFA Advice

* If a client allows Text Message based MFA you can attempt to Vish the code from the user
* While MFA is a strong mechanism, it is NOT infallible (Evilginx will help us to bypass MFA)
* SIM Swapping attacks and other vectors can lead to MFA codes being intercepted
* MFA Fatigue attacks are becoming more popular





## Avoid Spam Filters

* When possible, do not use words directly from vendor emails
* Do not copy Microsoft's email format or their website directly
* If you like HTLM editing this can be a great task for you
  * Avoid urgent words if you can, use spell check and AI tools if English is a second language
* Try to customize each pish to the client including their dialect and tendencies
* Your compromise is only as good as your OSINT and passive info gathering
  * Gather info on their common IT tools and the emails sent by those tools



## Prereq to success

For success in phishing today it's necessary to:

* Have your domain be alive for more than 30 days (many company filter new domains)
* Build trust for the domain by visiting the site
* Ensure the site is not categorized under malicious categories
* Domain takeover could be an option (also why Business Email Compromise is popular)

## Whitelisting Considerations

Work with your client to ensure that the domain/IP is whitelisted in multiple places:

* Email Gateway
* Spam Filter
* Domai Whitelist
* IP Address Whitelist

Send the email a few times and test directly with the client to ensure the email was received successfully.
