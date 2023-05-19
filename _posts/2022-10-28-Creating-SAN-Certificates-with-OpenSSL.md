---
title: "Creating SAN Certificates with OpenSSL"
date: 2022-10-28 10:00:00 -700
categories: [Linux]
tags: [linux, cmd, server, openssl, certificates, snippet]    #TAG names should always be lowercase
---
# Creating SAN Certificates with OpenSSL
I use this every time I need to create a certificate signing request (CSR) for SAN certificates and single-name certificates. This works for both

---

We will use __server01-san_cert.cnf__ for the config file, use whatever name makes sense for you

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

You should get prompted for the certificate information, like below
 
    Generating a RSA private key
    .............................................................+++++
    ...+++++
    writing new private key to 'server01-priv.key'
    Enter PEM pass phrase:
    Verifying - Enter PEM pass phrase:
    -----
    You are about to be asked to enter information that will be incorporated
    into your certificate request.
    What you are about to enter is what is called a Distinguished Name or a DN.
    There are quite a few fields but you can leave some blank
    For some fields there will be a default value,
    If you enter '.', the field will be left blank.
    -----
    Country Name (2 letter code) []:US
    State or Province Name (full name) []:California
    Locality Name (eg, city) []:San Diego
    Organizational Unit Name (eg, section) []:NotARealCompany
    Common Name (eg, your name or your server's hostname) []:server01.example.com
    Email Address []:admin@example.com
 

That will generate your certificate and key pair, which in our example should be
>server01_cert.csr  
>server01-priv.key

Next, we can verify that the CSR contains the alternate names we configured by running the following

```bash 
openssl req -noout -text -in server01_cert.csr | grep -A 1 "Subject Alternative Name"
X509v3 Subject Alternative Name:
    IP Address:10.1.1.1, IP Address:10.1.1.2, IP Address:10.1.1.3, DNS:server01a.example.com, DNS:server01b.example.com
```
---
And that's it. Go ahead and use that CSR to generate a certificate with your CA and then use the certificate and key (that you generated) to install onto a server or service.


-eof-