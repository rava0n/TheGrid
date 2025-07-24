# AVenger



## Ports Scanning

For this machine we have to use a more stealth command due AV enable.

```bash
nmap -sC -sV --min-rate 1000 10.10.57.181 -oN port_scan.txt

Nmap scan report for 10.10.57.181
Host is up (0.042s latency).
Not shown: 992 closed tcp ports (reset)
PORT     STATE SERVICE       VERSION
80/tcp   open  http          Apache httpd 2.4.56 (OpenSSL/1.1.1t PHP/8.0.28)
| http-ls: Volume /
| SIZE  TIME              FILENAME
| 3.5K  2022-06-15 16:07  applications.html
| 177   2022-06-15 16:07  bitnami.css
| -     2023-04-06 09:24  dashboard/
| 30K   2015-07-16 15:32  favicon.ico
| -     2023-06-27 09:26  gift/
| -     2023-06-27 09:04  img/
| 751   2022-06-15 16:07  img/module_table_bottom.png
| 337   2022-06-15 16:07  img/module_table_top.png
| -     2023-06-28 14:39  xampp/
|_
|_http-server-header: Apache/2.4.56 (Win64) OpenSSL/1.1.1t PHP/8.0.28
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-title: Index of /
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
443/tcp  open  ssl/http      Apache httpd 2.4.56 (OpenSSL/1.1.1t PHP/8.0.28)
| http-ls: Volume /
| SIZE  TIME              FILENAME
| 3.5K  2022-06-15 16:07  applications.html
| 177   2022-06-15 16:07  bitnami.css
| -     2023-04-06 09:24  dashboard/
| 30K   2015-07-16 15:32  favicon.ico
| -     2023-06-27 09:26  gift/
| -     2023-06-27 09:04  img/
| 751   2022-06-15 16:07  img/module_table_bottom.png
| 337   2022-06-15 16:07  img/module_table_top.png
| -     2023-06-28 14:39  xampp/
|_
|_http-server-header: Apache/2.4.56 (Win64) OpenSSL/1.1.1t PHP/8.0.28
| tls-alpn: 
|_  http/1.1
| http-methods: 
|_  Potentially risky methods: TRACE
| ssl-cert: Subject: commonName=localhost
| Not valid before: 2009-11-10T23:48:47
|_Not valid after:  2019-11-08T23:48:47
|_http-title: Index of /
|_ssl-date: TLS randomness does not represent time
445/tcp  open  microsoft-ds?
3306/tcp open  mysql         MariaDB 5.5.5-10.4.28
| mysql-info: 
|   Protocol: 10
|   Version: 5.5.5-10.4.28-MariaDB
|   Thread ID: 10
|   Capabilities flags: 63486
|   Some Capabilities: ODBCClient, Support41Auth, SupportsLoadDataLocal, Speaks41ProtocolNew, ConnectWithDatabase, SupportsCompression, SupportsTransactions, IgnoreSpaceBeforeParenthesis, FoundRows, LongColumnFlag, InteractiveClient, IgnoreSigpipes, DontAllowDatabaseTableColumn, Speaks41ProtocolOld, SupportsMultipleStatments, SupportsMultipleResults, SupportsAuthPlugins
|   Status: Autocommit
|   Salt: Nw207dp*|Vws\QbyX-w1
|_  Auth Plugin Name: mysql_native_password
3389/tcp open  ms-wbt-server Microsoft Terminal Services
| ssl-cert: Subject: commonName=gift
| Not valid before: 2025-07-23T20:24:16
|_Not valid after:  2026-01-22T20:24:16
| rdp-ntlm-info: 
|   Target_Name: GIFT
|   NetBIOS_Domain_Name: GIFT
|   NetBIOS_Computer_Name: GIFT
|   DNS_Domain_Name: gift
|   DNS_Computer_Name: gift
|   Product_Version: 10.0.17763
|_  System_Time: 2025-07-24T20:25:04+00:00
|_ssl-date: 2025-07-24T20:25:13+00:00; +1s from scanner time.
5985/tcp open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Not Found
|_http-server-header: Microsoft-HTTPAPI/2.0
Service Info: Hosts: localhost, www.example.com; OS: Windows; CPE: cpe:/o:microsoft:windows

```
