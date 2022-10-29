---
title: "Creating SAN Certificates with OpenSSL"
date: 2022-10-28 10:00:00 -700
categories: [snippet, openssl]
tags: [linux, cmd, server, openssl, certificates]    #TAG names should always be lowercase
---
# Creating SAN Certificates with OpenSSL
I use this every time I need to create a certificate signing request (CSR) for SAN certificates and single-name certificates. This works for both

> We will use `server01-san_cert.cnf` for the config file, use whatever name makes sense for you

I copy the code below into a new file, named for the server/service I am creating the CSR for.
