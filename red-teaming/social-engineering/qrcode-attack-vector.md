# QRCode Attack vector



## Generate a QRCode with a maliciuos URL

### QRCode Gen with SeToolKit

To generate a malicious QRCode we can use the SeToolKit&#x20;

{% code title="Installation SeToolKit" %}
```bash
sudo apt install set
```
{% endcode %}

Once installed, we can select the `QRCode Generator Attack Vector` from the main menu.

```bash
sudo setoolkit

set> 8
```

Now we have to insert the malicious URL.

```
Enter the URL you want the QRCode to go to (99 to exit): MAL_URL
```

The tool will generate a local path where the generated QRCode image is stored. \
Copy the image and use it responsibly.

<figure><img src="../../.gitbook/assets/image (240).png" alt=""><figcaption></figcaption></figure>
