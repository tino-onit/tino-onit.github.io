---
title: "SSL Snippets"
date: 2023-06-28 10:00:00 -700
categories: [Misc]
tags: [troubleshooting, cmd, linux, openssl, certificates, snippet]    #TAG names should always be lowercase
---

# SSL/TLS Snippets

Various commands I've used to help troubleshoot and/or check certificate issues on webserver, services, and devices.

## Creating a SAN certificate

SAN stands for “Subject Alternative Names” and this helps you to have a single certificate for multiple CN (Common Name). In some cases, organizations can decide to save costs by issuing SAN certs for multiple public facing websites and services

### Create the CSR

- Log into a server where you have OpenSSL installed. In most cased, this is going to be some flavor of Linux
- Navigate to which ever dir you want to work in
- Create a configuration file using any text editor. As an example, lets call this *san.cnf*. In this file, put the following information

```shell
[ req ]
default_bits       = 2048
distinguished_name = req_distinguished_name
req_extensions     = req_ext

[ req_distinguished_name ]
countryName                 = Country Name (2 letter code)
stateOrProvinceName         = State or Province Name (full name)
localityName               = Locality Name (eg, city)
organizationName           = Organization Name (eg, company)
commonName                 = Common Name (e.g. server FQDN or YOUR name)

[ req_ext ]
subjectAltName = @alt_names

[alt_names]
DNS.1   = tino-onit.github.io
DNS.2   = tino-onit.xyz
DNS.3   = tino-onit.net
IP.1    = 127.0.0.1
```

> **Note:** Settings in this file can be edited to you preference, e.g, bits, field texts and obviously the DNS names. You can also do IP's as I have shown above. Just to be clear, the "alt_names" section is the one you'll want to add those DNS names and/or IP's
{: .prompt-info }

Save the file and then execute the following OpenSSL command to generate the CSR and KEY file.

```shell
openssl req -out website-cert.csr -newkey rsa:2048 -notes -keyout website-priv.key -config san.cnf
```

This is going to create ```website-cert.csr``` and ```website-priv.key``` in the current dir. You will then send ```website-cert.csr``` to whomever the certificate signing authority is, so they can provide you with a (SAN) certificate.

### Verifying your CSR

Before sending your CSR off to the CA, you might to check it to ensure all the data is correct. You can do so with the following command

```shell
openssl req -noout -text -in website-cert.csr 
# or more specifically
openssl req -noout -text -in website-cert.csr | grep DNS
```

Which should output something like

```shell
[tino@server certs]# 
    DNS:tino-onit.github.io,DNS:tino-onit.xyz,DNS:tino-onit.net
```

Again, if you are happy with the results then you can go ahead and send it off to your signing authority to issue a certificate.

## Get certificate information

```shell
 curl --insecure -vvI https://example.com" 2>&1 | awk 'BEGIN { cert=0 } /^\* SSL connection/ { cert=1 } /^\*/ { if (cert) print }'
```

Should get you an output like below

```shell
* SSL connection using TLSv1.2 / ECDHE-RSA-AES256-GCM-SHA384
* ALPN, server accepted to use http/1.1
* Server certificate:
*  subject: C=US; ST=California; O=Example Company; CN=example.com
*  start date: Aug 28 00:00:00 2022 GMT
*  expire date: Aug 29 23:59:59 2023 GMT
*  issuer: C=US; O=DigiCert Inc; CN=DigiCert TLS RSA SHA256 2020 CA1
*  SSL certificate verify result: unable to get local issuer certificate (20), continuing anyway.
* TLSv1.2 (OUT), TLS header, Supplemental data (23):
* TLSv1.2 (IN), TLS header, Supplemental data (23):
* Mark bundle as not supporting multiuse
* Connection #0 to host example.com left intact
```

## Displaying Ciphers

This allows you view the supported ciphers

```shell
nmap --script +ssl-enum-ciphers -p 443 example.com
```

You should get something like this

```shell
Nmap scan report for exmaple.com (99.99.99.99)
Host is up (0.00071s latency).

PORT    STATE SERVICE
443/tcp open  https
| ssl-enum-ciphers:
|   TLSv1.1:
|     ciphers:
|       TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA (ecdh_x25519) - A
|       TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA (ecdh_x25519) - A
|       TLS_RSA_WITH_AES_256_CBC_SHA (rsa 2048) - A
|       TLS_RSA_WITH_AES_128_CBC_SHA (rsa 2048) - A
|       TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 (ecdh_x25519) - A
|       TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256 (ecdh_x25519) - A
|       TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384 (ecdh_x25519) - A
|       TLS_ECDHE_RSA_WITH_CHACHA20_POLY1305_SHA256 (ecdh_x25519) - A
|       TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384 (ecdh_x25519) - A
|       TLS_RSA_WITH_AES_128_GCM_SHA256 (rsa 2048) - A
|       TLS_RSA_WITH_AES_256_GCM_SHA384 (rsa 2048) - A
|       TLS_RSA_WITH_AES_128_CBC_SHA256 (rsa 2048) - A
|     compressors:
|       NULL
|     cipher preference: server
|   TLSv1.2:
|     ciphers:
|       TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 (ecdh_x25519) - A
|       TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256 (ecdh_x25519) - A
|       TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA (ecdh_x25519) - A
|       TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384 (ecdh_x25519) - A
|       TLS_ECDHE_RSA_WITH_CHACHA20_POLY1305_SHA256 (ecdh_x25519) - A
|       TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384 (ecdh_x25519) - A
|       TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA (ecdh_x25519) - A
|       TLS_RSA_WITH_AES_128_GCM_SHA256 (rsa 2048) - A
|       TLS_RSA_WITH_AES_256_GCM_SHA384 (rsa 2048) - A
|       TLS_RSA_WITH_AES_128_CBC_SHA256 (rsa 2048) - A
|       TLS_RSA_WITH_AES_256_CBC_SHA (rsa 2048) - A
|       TLS_RSA_WITH_AES_128_CBC_SHA (rsa 2048) - A
|     compressors:
|       NULL
|     cipher preference: server
|_  least strength: A

Nmap done: 1 IP address (1 host up) scanned in 0.50 seconds
```

Another way to display the ciphers (and if they are supported) is with this script I found online - sorry, I wish I could give credit, but I don't recall where I found it.

```shell
#!/usr/bin/env bash

# Script to test the ciphers that a URL/host uses
# OpenSSL requires the port number
# Usage: ./cipherscan.sh <IP>:<PORT>

SERVER=$1
DELAY=1
ciphers=$(openssl ciphers 'ALL:eNULL' | sed -e 's/:/ /g')

echo Obtaining cipher list from $(openssl version).

for cipher in ${ciphers[@]}
do
echo -n Testing $cipher...
result=$(echo -n | openssl s_client -cipher "$cipher" -connect $SERVER 2>&1)
if [[ "$result" =~ ":error:" ]] ; then
    error=$(echo -n $result | cut -d':' -f6)
    echo NO \($error\)
else
    if [[ "$result" =~ "Cipher is ${cipher}" || "$result" =~ "Cipher    :" ]] ; then
      echo YES
    else
      echo UKNOWN RESPONSE
      echo $result
    fi
fi
sleep $DELAY
done
```

Running the above script will give you results like the following (It will go through all the suites)

```shell
root@net-tools:~/Scripts# ./cipherscan.sh https://oscars.org
Obtaining cipher list from OpenSSL 3.0.2 15 Mar 2022 (Library: OpenSSL 3.0.2 15 Mar 2022).
...
Testing DHE-PSK-ARIA128-GCM-SHA256...NO (system lib)
Testing AES128-GCM-SHA256...YES (system lib)
Testing AES128-CCM8...NO (system lib)
Testing AES128-CCM...NO (system lib)
Testing ARIA128-GCM-SHA256...YES (system lib)
...
```

-eof-
