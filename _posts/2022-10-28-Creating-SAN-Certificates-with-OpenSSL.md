---
title: "Creating SAN Certificates with OpenSSL"
date: 2022-10-28 10:00:00 -700
categories: [snippet, openssl]
tags: [linux, cmd, server, openssl, certificates]    #TAG names should always be lowercase
---
# Creating SAN Certificates with OpenSSL
I use this every time I need to create a certificate signing request (CSR) for SAN certificates and single-name certificates. This works for both

> We will use __server01-san_cert.cnf__ for the config file, use whatever name makes sense for you

I copy the code below into a new file, named for the server/service I am creating the CSR for.


[req]
[req]
distinguished_name = req_distinguished_name
req_extensions = req_ext

[req_distinguished_name]
countryName                     = Country Name (2 letter code)
stateOrProvinceName             = State or Province Name (full name)
localityName                    = Locality Name (eg, city)
organizationalUnitName          = Organizational Unit Name (eg, section)
commonName                      = Common Name (eg, your name or your server\'s hostname)
emailAddress                    = Email Address

[req_ext]
subjectAltName = @alt_names

[alt_names]
IP.1 = 10.1.1.1
IP.2 = 10.1.1.2
IP.3 = 10.1.1.3
DNS.1 = server01a.example.com
DNS.2 = server01b.example.com


Next, use __openssl__ to generate the CSR and KEY pair, using the config file
```bash
openssl req -new -config server01-san_cert.cnf -out server01_cert.csr -keyout server01-priv.key
```