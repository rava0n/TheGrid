# GoPhish Campaign

## Create a sender profile

Go to the GoPhish dashboard and in the side bar click the "Sending Profiles" section.

Create New profile with this parameters:

* `Name`: name of the sending profile
* `SMTP From`: insert the Mailgun email created in the [Phishing Setups](phishing-setups.md#mailgun-setup) section.
* `Host`: insert the Mailgun SMTP host with the port 587\
  ![](<../../../.gitbook/assets/image (2).png>)\
  ![](<../../../.gitbook/assets/image (3).png>)
* `Username` and `Password`: mailgun email and his password\
  ![](<../../../.gitbook/assets/image (4).png>)



At the end of the Profile Sending configuration try to send a Test Email for a easy check of the configuration made.



## Create an Email Template

Click in the "Email Template" page and create a new email. Compile it with this parameter:

* Name: name of the Template
* Envelope sender: email that send the email (default the mail use the SMTP in the Sending Profile conf)
* Subject: set the email's subject
* Email Body: create the HTLM of the email. To add a link in the email we have to click the Link icon and set this conf. ( .URL indicates the landing page used by campaign)\
  ![](<../../../.gitbook/assets/image (5).png>)



## Create Landin page

Click in the "Landing Pages" section and create a new landing page for the phishing campaign. Compile it with this parameter:

* `Name`: insert the template name
* `HTML`: create or import the HTLM from a form to use (ex Google login, Outlook login, ...)
* `Capture Submitted Data`: set this option if we want get the password submitted
* `Redirect to`: insert the URL into the victim will be redirect after submitted the form.\
  ![](<../../../.gitbook/assets/image (6).png>)





## Create Users and Groups to send to

Click on "Users & Groups" section to create a group of victim emails.

<figure><img src="../../../.gitbook/assets/image (7).png" alt="" width="274"><figcaption></figcaption></figure>



## Start the Campaign

When all the configurations are done, click the "Campaigns" section and create a new campaign.

In the New Campaign form, select all components created previously.

<figure><img src="../../../.gitbook/assets/image (8).png" alt="" width="268"><figcaption></figcaption></figure>

The URL parameter is the Phishing Server for the landin page. (Page with the TLS certificate).

Now, we can Launch the Campaign.
