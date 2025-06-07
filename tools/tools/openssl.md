# openssl

**OpenSSL** is a cryptography toolkit implementing the Secure Sockets Layer (SSL) and Transport Layer Security (TLS) network protocols and related cryptography standards required by them.

The openssl program is a command line program for using the various cryptography functions of OpenSSL's crypto library from the shell. It can be used for

* Creation and management of private keys, public keys and parameters
* Public key cryptographic operations
* Creation of X.509 certificates, CSRs and CRLs
* Calculation of Message Digests and Message Authentication Codes
* Encryption and Decryption with Ciphers
* SSL/TLS Client and Server Tests
* Handling of S/MIME signed or encrypted mail
* Timestamp requests, generation and verification

## Convert a .pfx file to .pem

A `.pfx` file is a container format that combines a private key and a certificate, often used to authenticate a client or server. To use this file for accessing a Windows machine via WinRM, you need to convert it into a `.pem` file, which is a more widely accepted format for tools like OpenSSL.

```bash
openssl pkcs12 -in your-file.pfx -nocerts -out key.pem -nodes
openssl pkcs12 -in your-file.pfx -nokeys -out cert.pem

# -in: input .pfx file
# -nocerts: extracts only the private key, excluding the certifica
# -out: specifies output file
# -nodes: saves the private key without encyption 
```

With this files we can access via WinRM to Windows host with port 5895/96 open

[#id-5985-5986-winrm](../../pt/executive-pt/enumeration-exploitation/windows-enum-exploitation/#id-5985-5986-winrm "mention")

```bash
evil-winrm -i 10.10.11.152 -c cert.pem -k key.pem -S
```







