# Extracting the **certificate** and **keys** from a **.pfx** file
*Timelapse - hackthebox*

The **.pfx** file, which is in a **PKCS#12** format, contains the SSL certificate (public keys) and the corresponding private keys. Sometimes, you might have to import the certificate and private keys separately in an unencrypted plain text format to use it on another system.
## Extract .crt and .key files from .pfx file

Extract the private key (as private key)
```console
kali@kali:~$ openssl pkcs12 -in legacyy_dev_auth.pfx -nocerts -out prikey.key
```
Extract the certificate (as public key)
```console
kali@kali:~$ openssl pkcs12 -in legacyy_dev_auth.pfx -clcerts -nokeys -out cert.crt
```
Decrypt the private key
```console
kali@kali:~$ openssl rsa -in prikey.key -out de_prikey.key
```
## Convert .pfx file to .pem format
There might be instances where we might have to convert the **.pfx** file into **.pem** format.
```console
kali@kali:~$ openssl rsa -in prikey.key -outform PEM -out pem_prikey.key
```