---
title: "Palo Alto NGFW Day 1 Configuration"
date: 2022-10-14 10:00:00 -700
categories: [Palo Alto]
tags: [network, firewall, paloalto, config] #TAG names should always be lowercase
---
# Palo Alto NGFW Day 1 Config
 I always like to the use Palo Alto's Day 1 configuration file for when setting up new firewalls. The below text is copied from the Palo Alto site. Using Palo Alto's config assessment feature, this should net a score of 60%.

## What is Day 1 Config
---
What is a Day 1 Configuration, and what are the advantages of running it?

- The Day 1 Configuration tool helps you configure your devices for threat prevention using best practice recommendations from Palo Alto Networks.

Instead of extensive and detailed "how-to" documentation, Day 1 Configuration templates provide an easy-to-implement configuration model that is use case agnostic. The emphasis is on key security elements, such as: dynamic updates, security profiles, rules, and logging that should be consistent across deployments.

## Why Use Day 1 Configuration Templates?
---
Day 1 configuration templates play use common best practice recommendations and compiles them into pre-built Day 1 Configuration templates. These templates can then be loaded into Panorama or a next-generation firewall. Benefits of Day 1 Configuration templates include:

- Faster time to implement
- Reduce configuration errors
- Improve security posture

Once you register a new firewall, you will be presented with the option to download the Day 1 config.
![Sample](https://lh5.googleusercontent.com/iQoPA1-dVJjPbN2yXIHFr0N6mJZZIZy9u2OYkSvNBsn3mERA_X-LAqo2QN6oprzFIOTHmVxwKEf8Dt5TNcEMGn-2jXqZHeOXJssQ8pS1FOYUdHbSyWLVulBM-DRoVshp-opKxOQG)

Additionally, you can read more about the configuration [here, at the IronSkillet website](https://iron-skillet.readthedocs.io/en/docs_master/overview.html).

> If you already have a NGFW deployed but still want to take advantage of the various pieces of configuration, like the various Security Profiles, then below I have the code snippets I use 

# Individual Components

I have broken down the _in my opinion_, nicely constructed security profiles. You can use these as a basis for your Outbound, Inbound, and Internal security policies. This also includes SSL decryptions profiles.
## Custom URL Category Profiles
---
```bash
# -------------------- Custome URL categories I typycally use
set profiles custom-url-category Allow-URL-List type "URL List"
set profiles custom-url-category Allow-URL-List list [ *.paloaltonetworks.com/ ]
set profiles custom-url-category Block-URL-List type "URL List"
set profiles custom-url-category Block-URL-List list [ *.ru/ *.mega.io/ *.mega.co.nz/ *.mega.nz/ ]
set profiles custom-url-category Custom-Decrypt type "URL List"
set profiles custom-url-category Custom-Decrypt list [ *.badssl.com/ *.eicar.org/ ]
set profiles custom-url-category Custom-No-Decrypt-Expired type "URL List"
set profiles custom-url-category Custom-No-Decrypt type "URL List"
set profiles custom-url-category Allow-URL-Geo-Exception type "URL List"
```
## Decryption Profiles
---
```bash
# -------------------- Default Decryption
set profiles decryption Decryption_Profile ssl-forward-proxy block-expired-certificate yes
set profiles decryption Decryption_Profile ssl-forward-proxy block-untrusted-issuer yes
set profiles decryption Decryption_Profile ssl-forward-proxy block-unknown-cert yes
set profiles decryption Decryption_Profile ssl-forward-proxy block-timeout-cert yes
set profiles decryption Decryption_Profile ssl-forward-proxy block-unsupported-version yes
set profiles decryption Decryption_Profile ssl-forward-proxy block-unsupported-cipher yes
set profiles decryption Decryption_Profile ssl-no-proxy block-expired-certificate yes
set profiles decryption Decryption_Profile ssl-no-proxy block-untrusted-issuer yes
set profiles decryption Decryption_Profile ssl-inbound-proxy block-unsupported-version no
set profiles decryption Decryption_Profile ssl-inbound-proxy block-unsupported-cipher no
set profiles decryption Decryption_Profile ssh-proxy block-unsupported-version yes
set profiles decryption Decryption_Profile ssh-proxy block-unsupported-alg yes
set profiles decryption Decryption_Profile ssl-protocol-settings min-version tls1-2
set profiles decryption Decryption_Profile ssl-protocol-settings max-version max
set profiles decryption Decryption_Profile ssl-protocol-settings keyxchg-algo-rsa no
set profiles decryption Decryption_Profile ssl-protocol-settings enc-algo-3des no
set profiles decryption Decryption_Profile ssl-protocol-settings enc-algo-rc4 no
# -------------------- If Expired SSL
set profiles decryption Decryption_Profile ssl-protocol-settings auth-algo-sha1 no
set profiles decryption Decryption_Profile-Expired ssl-forward-proxy block-expired-certificate yes
set profiles decryption Decryption_Profile-Expired ssl-forward-proxy block-untrusted-issuer yes
set profiles decryption Decryption_Profile-Expired ssl-forward-proxy block-unknown-cert yes
set profiles decryption Decryption_Profile-Expired ssl-forward-proxy block-timeout-cert yes
set profiles decryption Decryption_Profile-Expired ssl-forward-proxy block-unsupported-version yes
set profiles decryption Decryption_Profile-Expired ssl-forward-proxy block-unsupported-cipher yes
set profiles decryption Decryption_Profile-Expired ssl-no-proxy block-expired-certificate no
set profiles decryption Decryption_Profile-Expired ssl-no-proxy block-untrusted-issuer no
set profiles decryption Decryption_Profile-Expired ssl-inbound-proxy block-unsupported-version no
set profiles decryption Decryption_Profile-Expired ssl-inbound-proxy block-unsupported-cipher no
set profiles decryption Decryption_Profile-Expired ssh-proxy block-unsupported-version yes
set profiles decryption Decryption_Profile-Expired ssh-proxy block-unsupported-alg yes
set profiles decryption Decryption_Profile-Expired ssl-protocol-settings min-version tls1-2
set profiles decryption Decryption_Profile-Expired ssl-protocol-settings max-version max
set profiles decryption Decryption_Profile-Expired ssl-protocol-settings keyxchg-algo-rsa no
set profiles decryption Decryption_Profile-Expired ssl-protocol-settings enc-algo-3des no
set profiles decryption Decryption_Profile-Expired ssl-protocol-settings enc-algo-rc4 no
set profiles decryption Decryption_Profile-Expired ssl-protocol-settings auth-algo-sha1 yes
```
## Antivirus Profiles
---
```bash
# -------------------- Alert-Only
set profiles virus Alert-Only-AV decoder ftp action alert
set profiles virus Alert-Only-AV decoder ftp wildfire-action alert
set profiles virus Alert-Only-AV decoder ftp mlav-action alert
set profiles virus Alert-Only-AV decoder http action alert
set profiles virus Alert-Only-AV decoder http wildfire-action alert
set profiles virus Alert-Only-AV decoder http mlav-action alert
set profiles virus Alert-Only-AV decoder http2 action alert
set profiles virus Alert-Only-AV decoder http2 wildfire-action alert
set profiles virus Alert-Only-AV decoder http2 mlav-action alert
set profiles virus Alert-Only-AV decoder imap action alert
set profiles virus Alert-Only-AV decoder imap wildfire-action alert
set profiles virus Alert-Only-AV decoder imap mlav-action alert
set profiles virus Alert-Only-AV decoder pop3 action alert
set profiles virus Alert-Only-AV decoder pop3 wildfire-action alert
set profiles virus Alert-Only-AV decoder pop3 mlav-action alert
set profiles virus Alert-Only-AV decoder smb action alert
set profiles virus Alert-Only-AV decoder smb wildfire-action alert
set profiles virus Alert-Only-AV decoder smb mlav-action alert
set profiles virus Alert-Only-AV decoder smtp action alert
set profiles virus Alert-Only-AV decoder smtp wildfire-action alert
set profiles virus Alert-Only-AV decoder smtp mlav-action alert
set profiles virus Alert-Only-AV mlav-engine-filebased-enabled "Windows Executables" mlav-policy-action enable(alert-only)
set profiles virus Alert-Only-AV mlav-engine-filebased-enabled "PowerShell Script 1" mlav-policy-action enable(alert-only)
set profiles virus Alert-Only-AV mlav-engine-filebased-enabled "PowerShell Script 2" mlav-policy-action enable(alert-only)
# -------------------- Outbound
set profiles virus Outbound-AV decoder ftp action reset-both
set profiles virus Outbound-AV decoder ftp wildfire-action reset-both
set profiles virus Outbound-AV decoder ftp mlav-action reset-both
set profiles virus Outbound-AV decoder http action reset-both
set profiles virus Outbound-AV decoder http wildfire-action reset-both
set profiles virus Outbound-AV decoder http mlav-action reset-both
set profiles virus Outbound-AV decoder http2 action reset-both
set profiles virus Outbound-AV decoder http2 wildfire-action reset-both
set profiles virus Outbound-AV decoder http2 mlav-action reset-both
set profiles virus Outbound-AV decoder imap action reset-both
set profiles virus Outbound-AV decoder imap wildfire-action reset-both
set profiles virus Outbound-AV decoder imap mlav-action reset-both
set profiles virus Outbound-AV decoder pop3 action reset-both
set profiles virus Outbound-AV decoder pop3 wildfire-action reset-both
set profiles virus Outbound-AV decoder pop3 mlav-action reset-both
set profiles virus Outbound-AV decoder smb action reset-both
set profiles virus Outbound-AV decoder smb wildfire-action reset-both
set profiles virus Outbound-AV decoder smb mlav-action reset-both
set profiles virus Outbound-AV decoder smtp action reset-both
set profiles virus Outbound-AV decoder smtp wildfire-action reset-both
set profiles virus Outbound-AV decoder smtp mlav-action reset-both
set profiles virus Outbound-AV mlav-engine-filebased-enabled "Windows Executables" mlav-policy-action enable
set profiles virus Outbound-AV mlav-engine-filebased-enabled "PowerShell Script 1" mlav-policy-action enable
set profiles virus Outbound-AV mlav-engine-filebased-enabled "PowerShell Script 2" mlav-policy-action enable
# -------------------- Inbound
set profiles virus Inbound-AV decoder ftp action reset-both
set profiles virus Inbound-AV decoder ftp wildfire-action reset-both
set profiles virus Inbound-AV decoder ftp mlav-action reset-both
set profiles virus Inbound-AV decoder http action reset-both
set profiles virus Inbound-AV decoder http wildfire-action reset-both
set profiles virus Inbound-AV decoder http mlav-action reset-both
set profiles virus Inbound-AV decoder http2 action reset-both
set profiles virus Inbound-AV decoder http2 wildfire-action reset-both
set profiles virus Inbound-AV decoder http2 mlav-action reset-both
set profiles virus Inbound-AV decoder imap action reset-both
set profiles virus Inbound-AV decoder imap wildfire-action reset-both
set profiles virus Inbound-AV decoder imap mlav-action reset-both
set profiles virus Inbound-AV decoder pop3 action reset-both
set profiles virus Inbound-AV decoder pop3 wildfire-action reset-both
set profiles virus Inbound-AV decoder pop3 mlav-action reset-both
set profiles virus Inbound-AV decoder smb action reset-both
set profiles virus Inbound-AV decoder smb wildfire-action reset-both
set profiles virus Inbound-AV decoder smb mlav-action reset-both
set profiles virus Inbound-AV decoder smtp action reset-both
set profiles virus Inbound-AV decoder smtp wildfire-action reset-both
set profiles virus Inbound-AV decoder smtp mlav-action reset-both
set profiles virus Inbound-AV mlav-engine-filebased-enabled "Windows Executables" mlav-policy-action enable
set profiles virus Inbound-AV mlav-engine-filebased-enabled "PowerShell Script 1" mlav-policy-action enable
set profiles virus Inbound-AV mlav-engine-filebased-enabled "PowerShell Script 2" mlav-policy-action enable
# -------------------- Internal
set profiles virus Internal-AV decoder ftp action reset-both
set profiles virus Internal-AV decoder ftp wildfire-action reset-both
set profiles virus Internal-AV decoder ftp mlav-action reset-both
set profiles virus Internal-AV decoder http action reset-both
set profiles virus Internal-AV decoder http wildfire-action reset-both
set profiles virus Internal-AV decoder http mlav-action reset-both
set profiles virus Internal-AV decoder http2 action reset-both
set profiles virus Internal-AV decoder http2 wildfire-action reset-both
set profiles virus Internal-AV decoder http2 mlav-action reset-both
set profiles virus Internal-AV decoder imap action reset-both
set profiles virus Internal-AV decoder imap wildfire-action reset-both
set profiles virus Internal-AV decoder imap mlav-action reset-both
set profiles virus Internal-AV decoder pop3 action reset-both
set profiles virus Internal-AV decoder pop3 wildfire-action reset-both
set profiles virus Internal-AV decoder pop3 mlav-action reset-both
set profiles virus Internal-AV decoder smb action reset-both
set profiles virus Internal-AV decoder smb wildfire-action reset-both
set profiles virus Internal-AV decoder smb mlav-action reset-both
set profiles virus Internal-AV decoder smtp action reset-both
set profiles virus Internal-AV decoder smtp wildfire-action reset-both
set profiles virus Internal-AV decoder smtp mlav-action reset-both
set profiles virus Internal-AV mlav-engine-filebased-enabled "Windows Executables" mlav-policy-action enable
set profiles virus Internal-AV mlav-engine-filebased-enabled "PowerShell Script 1" mlav-policy-action enable
set profiles virus Internal-AV mlav-engine-filebased-enabled "PowerShell Script 2" mlav-policy-action enable
# -------------------- Exception
set profiles virus Exception-AV decoder ftp action reset-both
set profiles virus Exception-AV decoder ftp wildfire-action reset-both
set profiles virus Exception-AV decoder ftp mlav-action reset-both
set profiles virus Exception-AV decoder http action reset-both
set profiles virus Exception-AV decoder http wildfire-action reset-both
set profiles virus Exception-AV decoder http mlav-action reset-both
set profiles virus Exception-AV decoder http2 action reset-both
set profiles virus Exception-AV decoder http2 wildfire-action reset-both
set profiles virus Exception-AV decoder http2 mlav-action reset-both
set profiles virus Exception-AV decoder imap action reset-both
set profiles virus Exception-AV decoder imap wildfire-action reset-both
set profiles virus Exception-AV decoder imap mlav-action reset-both
set profiles virus Exception-AV decoder pop3 action reset-both
set profiles virus Exception-AV decoder pop3 wildfire-action reset-both
set profiles virus Exception-AV decoder pop3 mlav-action reset-both
set profiles virus Exception-AV decoder smb action reset-both
set profiles virus Exception-AV decoder smb wildfire-action reset-both
set profiles virus Exception-AV decoder smb mlav-action reset-both
set profiles virus Exception-AV decoder smtp action reset-both
set profiles virus Exception-AV decoder smtp wildfire-action reset-both
set profiles virus Exception-AV decoder smtp mlav-action reset-both
set profiles virus Exception-AV mlav-engine-filebased-enabled "Windows Executables" mlav-policy-action enable
set profiles virus Exception-AV mlav-engine-filebased-enabled "PowerShell Script 1" mlav-policy-action enable
set profiles virus Exception-AV mlav-engine-filebased-enabled "PowerShell Script 2" mlav-policy-action enable
set profiles virus Exception-AV description "Use this profile for rules needing modifications to the standard"
```
## Anti-spyware Profiles
---
```bash
# -------------------- Outbound
set profiles spyware Outbound-AS botnet-domains lists default-paloalto-dns action sinkhole
set profiles spyware Outbound-AS botnet-domains lists default-paloalto-dns packet-capture disable
set profiles spyware Outbound-AS botnet-domains dns-security-categories pan-dns-sec-adtracking log-level default
set profiles spyware Outbound-AS botnet-domains dns-security-categories pan-dns-sec-adtracking action default
set profiles spyware Outbound-AS botnet-domains dns-security-categories pan-dns-sec-adtracking packet-capture disable
set profiles spyware Outbound-AS botnet-domains dns-security-categories pan-dns-sec-cc log-level default
set profiles spyware Outbound-AS botnet-domains dns-security-categories pan-dns-sec-cc action sinkhole
set profiles spyware Outbound-AS botnet-domains dns-security-categories pan-dns-sec-cc packet-capture disable
set profiles spyware Outbound-AS botnet-domains dns-security-categories pan-dns-sec-ddns log-level default
set profiles spyware Outbound-AS botnet-domains dns-security-categories pan-dns-sec-ddns action default
set profiles spyware Outbound-AS botnet-domains dns-security-categories pan-dns-sec-ddns packet-capture disable
set profiles spyware Outbound-AS botnet-domains dns-security-categories pan-dns-sec-grayware log-level default
set profiles spyware Outbound-AS botnet-domains dns-security-categories pan-dns-sec-grayware action default
set profiles spyware Outbound-AS botnet-domains dns-security-categories pan-dns-sec-grayware packet-capture disable
set profiles spyware Outbound-AS botnet-domains dns-security-categories pan-dns-sec-malware log-level default
set profiles spyware Outbound-AS botnet-domains dns-security-categories pan-dns-sec-malware action sinkhole
set profiles spyware Outbound-AS botnet-domains dns-security-categories pan-dns-sec-malware packet-capture disable
set profiles spyware Outbound-AS botnet-domains dns-security-categories pan-dns-sec-parked log-level default
set profiles spyware Outbound-AS botnet-domains dns-security-categories pan-dns-sec-parked action default
set profiles spyware Outbound-AS botnet-domains dns-security-categories pan-dns-sec-parked packet-capture disable
set profiles spyware Outbound-AS botnet-domains dns-security-categories pan-dns-sec-phishing log-level default
set profiles spyware Outbound-AS botnet-domains dns-security-categories pan-dns-sec-phishing action default
set profiles spyware Outbound-AS botnet-domains dns-security-categories pan-dns-sec-phishing packet-capture disable
set profiles spyware Outbound-AS botnet-domains dns-security-categories pan-dns-sec-proxy log-level default
set profiles spyware Outbound-AS botnet-domains dns-security-categories pan-dns-sec-proxy action default
set profiles spyware Outbound-AS botnet-domains dns-security-categories pan-dns-sec-proxy packet-capture disable
set profiles spyware Outbound-AS botnet-domains dns-security-categories pan-dns-sec-recent log-level default
set profiles spyware Outbound-AS botnet-domains dns-security-categories pan-dns-sec-recent action default
set profiles spyware Outbound-AS botnet-domains dns-security-categories pan-dns-sec-recent packet-capture disable
set profiles spyware Outbound-AS botnet-domains sinkhole ipv4-address 72.5.65.111
set profiles spyware Outbound-AS botnet-domains sinkhole ipv6-address 2600:5200::1
set profiles spyware Outbound-AS botnet-domains whitelist login-microsoft.com description "Whitelisting Email Phishing Campaign"
set profiles spyware Outbound-AS rules Block-Critical-High-Medium action reset-both
set profiles spyware Outbound-AS rules Block-Critical-High-Medium severity [ critical high medium ]
set profiles spyware Outbound-AS rules Block-Critical-High-Medium threat-name any
set profiles spyware Outbound-AS rules Block-Critical-High-Medium category any
set profiles spyware Outbound-AS rules Block-Critical-High-Medium packet-capture single-packet
set profiles spyware Outbound-AS rules Default-Low-Info action default
set profiles spyware Outbound-AS rules Default-Low-Info severity [ low informational ]
set profiles spyware Outbound-AS rules Default-Low-Info threat-name any
set profiles spyware Outbound-AS rules Default-Low-Info category any
set profiles spyware Outbound-AS rules Default-Low-Info packet-capture disable
# -------------------- Inbound
set profiles spyware Inbound-AS botnet-domains lists default-paloalto-dns action sinkhole
set profiles spyware Inbound-AS botnet-domains lists default-paloalto-dns packet-capture disable
set profiles spyware Inbound-AS botnet-domains dns-security-categories pan-dns-sec-cc log-level default
set profiles spyware Inbound-AS botnet-domains dns-security-categories pan-dns-sec-cc action sinkhole
set profiles spyware Inbound-AS botnet-domains dns-security-categories pan-dns-sec-cc packet-capture disable
set profiles spyware Inbound-AS botnet-domains dns-security-categories pan-dns-sec-ddns log-level default
set profiles spyware Inbound-AS botnet-domains dns-security-categories pan-dns-sec-ddns action default
set profiles spyware Inbound-AS botnet-domains dns-security-categories pan-dns-sec-ddns packet-capture disable
set profiles spyware Inbound-AS botnet-domains dns-security-categories pan-dns-sec-grayware log-level default
set profiles spyware Inbound-AS botnet-domains dns-security-categories pan-dns-sec-grayware action default
set profiles spyware Inbound-AS botnet-domains dns-security-categories pan-dns-sec-grayware packet-capture disable
set profiles spyware Inbound-AS botnet-domains dns-security-categories pan-dns-sec-malware log-level default
set profiles spyware Inbound-AS botnet-domains dns-security-categories pan-dns-sec-malware action sinkhole
set profiles spyware Inbound-AS botnet-domains dns-security-categories pan-dns-sec-malware packet-capture disable
set profiles spyware Inbound-AS botnet-domains dns-security-categories pan-dns-sec-parked log-level default
set profiles spyware Inbound-AS botnet-domains dns-security-categories pan-dns-sec-parked action default
set profiles spyware Inbound-AS botnet-domains dns-security-categories pan-dns-sec-parked packet-capture disable
set profiles spyware Inbound-AS botnet-domains dns-security-categories pan-dns-sec-phishing log-level default
set profiles spyware Inbound-AS botnet-domains dns-security-categories pan-dns-sec-phishing action default
set profiles spyware Inbound-AS botnet-domains dns-security-categories pan-dns-sec-phishing packet-capture disable
set profiles spyware Inbound-AS botnet-domains dns-security-categories pan-dns-sec-proxy log-level default
set profiles spyware Inbound-AS botnet-domains dns-security-categories pan-dns-sec-proxy action default
set profiles spyware Inbound-AS botnet-domains dns-security-categories pan-dns-sec-proxy packet-capture disable
set profiles spyware Inbound-AS botnet-domains dns-security-categories pan-dns-sec-recent log-level default
set profiles spyware Inbound-AS botnet-domains dns-security-categories pan-dns-sec-recent action default
set profiles spyware Inbound-AS botnet-domains dns-security-categories pan-dns-sec-recent packet-capture disable
set profiles spyware Inbound-AS botnet-domains sinkhole ipv4-address 72.5.65.111
set profiles spyware Inbound-AS botnet-domains sinkhole ipv6-address 2600:5200::1
set profiles spyware Inbound-AS rules Block-Critical-High-Medium action reset-both
set profiles spyware Inbound-AS rules Block-Critical-High-Medium severity [ critical high medium ]
set profiles spyware Inbound-AS rules Block-Critical-High-Medium threat-name any
set profiles spyware Inbound-AS rules Block-Critical-High-Medium category any
set profiles spyware Inbound-AS rules Block-Critical-High-Medium packet-capture single-packet
set profiles spyware Inbound-AS rules Default-Low-Info action default
set profiles spyware Inbound-AS rules Default-Low-Info severity [ low informational ]
set profiles spyware Inbound-AS rules Default-Low-Info threat-name any
set profiles spyware Inbound-AS rules Default-Low-Info category any
set profiles spyware Inbound-AS rules Default-Low-Info packet-capture disable
# -------------------- Internal
set profiles spyware Internal-AS botnet-domains lists default-paloalto-dns action sinkhole
set profiles spyware Internal-AS botnet-domains lists default-paloalto-dns packet-capture disable
set profiles spyware Internal-AS botnet-domains dns-security-categories pan-dns-sec-cc log-level default
set profiles spyware Internal-AS botnet-domains dns-security-categories pan-dns-sec-cc action sinkhole
set profiles spyware Internal-AS botnet-domains dns-security-categories pan-dns-sec-cc packet-capture disable
set profiles spyware Internal-AS botnet-domains dns-security-categories pan-dns-sec-ddns log-level default
set profiles spyware Internal-AS botnet-domains dns-security-categories pan-dns-sec-ddns action default
set profiles spyware Internal-AS botnet-domains dns-security-categories pan-dns-sec-ddns packet-capture single-packet
set profiles spyware Internal-AS botnet-domains dns-security-categories pan-dns-sec-grayware log-level default
set profiles spyware Internal-AS botnet-domains dns-security-categories pan-dns-sec-grayware action default
set profiles spyware Internal-AS botnet-domains dns-security-categories pan-dns-sec-grayware packet-capture disable
set profiles spyware Internal-AS botnet-domains dns-security-categories pan-dns-sec-malware log-level default
set profiles spyware Internal-AS botnet-domains dns-security-categories pan-dns-sec-malware action sinkhole
set profiles spyware Internal-AS botnet-domains dns-security-categories pan-dns-sec-malware packet-capture disable
set profiles spyware Internal-AS botnet-domains dns-security-categories pan-dns-sec-parked log-level default
set profiles spyware Internal-AS botnet-domains dns-security-categories pan-dns-sec-parked action default
set profiles spyware Internal-AS botnet-domains dns-security-categories pan-dns-sec-parked packet-capture disable
set profiles spyware Internal-AS botnet-domains dns-security-categories pan-dns-sec-phishing log-level default
set profiles spyware Internal-AS botnet-domains dns-security-categories pan-dns-sec-phishing action default
set profiles spyware Internal-AS botnet-domains dns-security-categories pan-dns-sec-phishing packet-capture disable
set profiles spyware Internal-AS botnet-domains dns-security-categories pan-dns-sec-proxy log-level default
set profiles spyware Internal-AS botnet-domains dns-security-categories pan-dns-sec-proxy action default
set profiles spyware Internal-AS botnet-domains dns-security-categories pan-dns-sec-proxy packet-capture disable
set profiles spyware Internal-AS botnet-domains dns-security-categories pan-dns-sec-recent log-level default
set profiles spyware Internal-AS botnet-domains dns-security-categories pan-dns-sec-recent action default
set profiles spyware Internal-AS botnet-domains dns-security-categories pan-dns-sec-recent packet-capture disable
set profiles spyware Internal-AS botnet-domains sinkhole ipv4-address 72.5.65.111
set profiles spyware Internal-AS botnet-domains sinkhole ipv6-address 2600:5200::1
set profiles spyware Internal-AS rules Block-Critical-High action reset-both
set profiles spyware Internal-AS rules Block-Critical-High severity [ critical high ]
set profiles spyware Internal-AS rules Block-Critical-High threat-name any
set profiles spyware Internal-AS rules Block-Critical-High category any
set profiles spyware Internal-AS rules Block-Critical-High packet-capture single-packet
set profiles spyware Internal-AS rules Default-Medium-Low-Info action default
set profiles spyware Internal-AS rules Default-Medium-Low-Info severity [ medium low informational ]
set profiles spyware Internal-AS rules Default-Medium-Low-Info threat-name any
set profiles spyware Internal-AS rules Default-Medium-Low-Info category any
set profiles spyware Internal-AS rules Default-Medium-Low-Info packet-capture disable
# -------------------- Alert-Only
set profiles spyware Alert-Only-AS botnet-domains lists default-paloalto-dns action alert
set profiles spyware Alert-Only-AS botnet-domains lists default-paloalto-dns packet-capture disable
set profiles spyware Alert-Only-AS botnet-domains dns-security-categories pan-dns-sec-cc log-level default
set profiles spyware Alert-Only-AS botnet-domains dns-security-categories pan-dns-sec-cc action allow
set profiles spyware Alert-Only-AS botnet-domains dns-security-categories pan-dns-sec-cc packet-capture disable
set profiles spyware Alert-Only-AS botnet-domains dns-security-categories pan-dns-sec-ddns log-level default
set profiles spyware Alert-Only-AS botnet-domains dns-security-categories pan-dns-sec-ddns action allow
set profiles spyware Alert-Only-AS botnet-domains dns-security-categories pan-dns-sec-ddns packet-capture single-packet
set profiles spyware Alert-Only-AS botnet-domains dns-security-categories pan-dns-sec-grayware log-level default
set profiles spyware Alert-Only-AS botnet-domains dns-security-categories pan-dns-sec-grayware action default
set profiles spyware Alert-Only-AS botnet-domains dns-security-categories pan-dns-sec-grayware packet-capture disable
set profiles spyware Alert-Only-AS botnet-domains dns-security-categories pan-dns-sec-malware log-level default
set profiles spyware Alert-Only-AS botnet-domains dns-security-categories pan-dns-sec-malware action allow
set profiles spyware Alert-Only-AS botnet-domains dns-security-categories pan-dns-sec-malware packet-capture disable
set profiles spyware Alert-Only-AS botnet-domains dns-security-categories pan-dns-sec-parked log-level default
set profiles spyware Alert-Only-AS botnet-domains dns-security-categories pan-dns-sec-parked action default
set profiles spyware Alert-Only-AS botnet-domains dns-security-categories pan-dns-sec-parked packet-capture disable
set profiles spyware Alert-Only-AS botnet-domains dns-security-categories pan-dns-sec-phishing log-level default
set profiles spyware Alert-Only-AS botnet-domains dns-security-categories pan-dns-sec-phishing action default
set profiles spyware Alert-Only-AS botnet-domains dns-security-categories pan-dns-sec-phishing packet-capture disable
set profiles spyware Alert-Only-AS botnet-domains dns-security-categories pan-dns-sec-proxy log-level default
set profiles spyware Alert-Only-AS botnet-domains dns-security-categories pan-dns-sec-proxy action default
set profiles spyware Alert-Only-AS botnet-domains dns-security-categories pan-dns-sec-proxy packet-capture disable
set profiles spyware Alert-Only-AS botnet-domains dns-security-categories pan-dns-sec-recent log-level default
set profiles spyware Alert-Only-AS botnet-domains dns-security-categories pan-dns-sec-recent action allow
set profiles spyware Alert-Only-AS botnet-domains dns-security-categories pan-dns-sec-recent packet-capture disable
set profiles spyware Alert-Only-AS botnet-domains sinkhole ipv4-address 72.5.65.111
set profiles spyware Alert-Only-AS botnet-domains sinkhole ipv6-address 2600:5200::1
set profiles spyware Alert-Only-AS rules Alert-All action alert
set profiles spyware Alert-Only-AS rules Alert-All severity any
set profiles spyware Alert-Only-AS rules Alert-All threat-name any
set profiles spyware Alert-Only-AS rules Alert-All category any
set profiles spyware Alert-Only-AS rules Alert-All packet-capture disable
# -------------------- Exception
set profiles spyware Exception-AS description "Used for exception cases not part of the recommended practice"
set profiles spyware Exception-AS botnet-domains lists default-paloalto-dns packet-capture single-packet
set profiles spyware Exception-AS botnet-domains lists default-paloalto-dns action sinkhole
set profiles spyware Exception-AS botnet-domains dns-security-categories pan-dns-sec-benign log-level default
set profiles spyware Exception-AS botnet-domains dns-security-categories pan-dns-sec-benign action default
set profiles spyware Exception-AS botnet-domains dns-security-categories pan-dns-sec-benign packet-capture disable
set profiles spyware Exception-AS botnet-domains dns-security-categories pan-dns-sec-cc log-level default
set profiles spyware Exception-AS botnet-domains dns-security-categories pan-dns-sec-cc action default
set profiles spyware Exception-AS botnet-domains dns-security-categories pan-dns-sec-cc packet-capture disable
set profiles spyware Exception-AS botnet-domains dns-security-categories pan-dns-sec-ddns log-level default
set profiles spyware Exception-AS botnet-domains dns-security-categories pan-dns-sec-ddns action default
set profiles spyware Exception-AS botnet-domains dns-security-categories pan-dns-sec-ddns packet-capture disable
set profiles spyware Exception-AS botnet-domains dns-security-categories pan-dns-sec-malware log-level default
set profiles spyware Exception-AS botnet-domains dns-security-categories pan-dns-sec-malware action default
set profiles spyware Exception-AS botnet-domains dns-security-categories pan-dns-sec-malware packet-capture disable
set profiles spyware Exception-AS botnet-domains dns-security-categories pan-dns-sec-recent log-level default
set profiles spyware Exception-AS botnet-domains dns-security-categories pan-dns-sec-recent action default
set profiles spyware Exception-AS botnet-domains dns-security-categories pan-dns-sec-recent packet-capture disable
set profiles spyware Exception-AS botnet-domains sinkhole ipv4-address 72.5.65.111
set profiles spyware Exception-AS botnet-domains sinkhole ipv6-address 2600:5200::1
```
## Vulnerability
---
```bash
# -------------------- Outbound
set profiles vulnerability Outbound-VP rules Block-Critical-High-Medium action reset-both
set profiles vulnerability Outbound-VP rules Block-Critical-High-Medium vendor-id any
set profiles vulnerability Outbound-VP rules Block-Critical-High-Medium severity [ critical high medium ]
set profiles vulnerability Outbound-VP rules Block-Critical-High-Medium cve any
set profiles vulnerability Outbound-VP rules Block-Critical-High-Medium threat-name any
set profiles vulnerability Outbound-VP rules Block-Critical-High-Medium host any
set profiles vulnerability Outbound-VP rules Block-Critical-High-Medium category any
set profiles vulnerability Outbound-VP rules Block-Critical-High-Medium packet-capture single-packet
set profiles vulnerability Outbound-VP rules Default-Low-Info action default
set profiles vulnerability Outbound-VP rules Default-Low-Info vendor-id any
set profiles vulnerability Outbound-VP rules Default-Low-Info severity [ low informational ]
set profiles vulnerability Outbound-VP rules Default-Low-Info cve any
set profiles vulnerability Outbound-VP rules Default-Low-Info threat-name any
set profiles vulnerability Outbound-VP rules Default-Low-Info host any
set profiles vulnerability Outbound-VP rules Default-Low-Info category any
set profiles vulnerability Outbound-VP rules Default-Low-Info packet-capture disable
set profiles vulnerability Outbound-VP threat-exception 35223 action default
# -------------------- Inbound
set profiles vulnerability Inbound-VP rules Block-Critical-High-Medium action reset-both
set profiles vulnerability Inbound-VP rules Block-Critical-High-Medium vendor-id any
set profiles vulnerability Inbound-VP rules Block-Critical-High-Medium severity [ critical high medium ]
set profiles vulnerability Inbound-VP rules Block-Critical-High-Medium cve any
set profiles vulnerability Inbound-VP rules Block-Critical-High-Medium threat-name any
set profiles vulnerability Inbound-VP rules Block-Critical-High-Medium host any
set profiles vulnerability Inbound-VP rules Block-Critical-High-Medium category any
set profiles vulnerability Inbound-VP rules Block-Critical-High-Medium packet-capture single-packet
set profiles vulnerability Inbound-VP rules Default-Low-Info action default
set profiles vulnerability Inbound-VP rules Default-Low-Info vendor-id any
set profiles vulnerability Inbound-VP rules Default-Low-Info severity [ low informational ]
set profiles vulnerability Inbound-VP rules Default-Low-Info cve any
set profiles vulnerability Inbound-VP rules Default-Low-Info threat-name any
set profiles vulnerability Inbound-VP rules Default-Low-Info host any
set profiles vulnerability Inbound-VP rules Default-Low-Info category any
set profiles vulnerability Inbound-VP rules Default-Low-Info packet-capture disable
# -------------------- Internal
set profiles vulnerability Internal-VP rules Block-Critical-High action reset-both
set profiles vulnerability Internal-VP rules Block-Critical-High vendor-id any
set profiles vulnerability Internal-VP rules Block-Critical-High severity [ critical high ]
set profiles vulnerability Internal-VP rules Block-Critical-High cve any
set profiles vulnerability Internal-VP rules Block-Critical-High threat-name any
set profiles vulnerability Internal-VP rules Block-Critical-High host any
set profiles vulnerability Internal-VP rules Block-Critical-High category any
set profiles vulnerability Internal-VP rules Block-Critical-High packet-capture single-packet
set profiles vulnerability Internal-VP rules Default-Medium-Low-Info action default
set profiles vulnerability Internal-VP rules Default-Medium-Low-Info vendor-id any
set profiles vulnerability Internal-VP rules Default-Medium-Low-Info severity [ low informational medium ]
set profiles vulnerability Internal-VP rules Default-Medium-Low-Info cve any
set profiles vulnerability Internal-VP rules Default-Medium-Low-Info threat-name any
set profiles vulnerability Internal-VP rules Default-Medium-Low-Info host any
set profiles vulnerability Internal-VP rules Default-Medium-Low-Info category any
set profiles vulnerability Internal-VP rules Default-Medium-Low-Info packet-capture disable
set profiles vulnerability Internal-VP threat-exception 35223 action default
set profiles vulnerability Internal-VP threat-exception 59323 action allow
# -------------------- Alert-Only
set profiles vulnerability Alert-Only-VP rules Alert-All action alert
set profiles vulnerability Alert-Only-VP rules Alert-All vendor-id any
set profiles vulnerability Alert-Only-VP rules Alert-All severity any
set profiles vulnerability Alert-Only-VP rules Alert-All cve any
set profiles vulnerability Alert-Only-VP rules Alert-All threat-name any
set profiles vulnerability Alert-Only-VP rules Alert-All host any
set profiles vulnerability Alert-Only-VP rules Alert-All category any
set profiles vulnerability Alert-Only-VP rules Alert-All packet-capture disable
set profiles vulnerability Alert-Only-VP threat-exception 59323 action allow
# -------------------- Exception
set profiles vulnerability Exception-VP
```
## File-Blocking Profiles
---
```bash
# -------------------- Outbound
set profiles file-blocking Outbound-FB rules Alert-All application any
set profiles file-blocking Outbound-FB rules Alert-All direction both
set profiles file-blocking Outbound-FB rules Alert-All action alert
set profiles file-blocking Outbound-FB rules Alert-All file-type any
set profiles file-blocking Outbound-FB rules Block application any
set profiles file-blocking Outbound-FB rules Block direction both
set profiles file-blocking Outbound-FB rules Block action block
set profiles file-blocking Outbound-FB rules Block file-type [ 7z bat chm class cpl dll hlp hta jar ocx pif scr torrent vbe wsf ]
# -------------------- Inbound
set profiles file-blocking Inbound-FB rules Alert-All application any
set profiles file-blocking Inbound-FB rules Alert-All direction both
set profiles file-blocking Inbound-FB rules Alert-All action alert
set profiles file-blocking Inbound-FB rules Alert-All file-type any
set profiles file-blocking Inbound-FB rules Block application any
set profiles file-blocking Inbound-FB rules Block direction both
set profiles file-blocking Inbound-FB rules Block action block
set profiles file-blocking Inbound-FB rules Block file-type [ 7z bat chm class cpl dll hlp hta jar ocx pif scr torrent vbe wsf ]
set profiles file-blocking Internal-FB rules Alert-All application any
set profiles file-blocking Internal-FB rules Alert-All direction both
set profiles file-blocking Internal-FB rules Alert-All action alert
set profiles file-blocking Internal-FB rules Alert-All file-type any
set profiles file-blocking Internal-FB rules Block application any
set profiles file-blocking Internal-FB rules Block direction both
set profiles file-blocking Internal-FB rules Block action block
set profiles file-blocking Internal-FB rules Block file-type [ 7z bat chm class cpl hlp hta jar ocx pif scr torrent vbe wsf ]
# -------------------- Alert-Only
set profiles file-blocking Alert-Only-FB rules Alert-Only application any
set profiles file-blocking Alert-Only-FB rules Alert-Only direction both
set profiles file-blocking Alert-Only-FB rules Alert-Only action alert
set profiles file-blocking Alert-Only-FB rules Alert-Only file-type any
```
## URL Filtering Profiles
---
```bash
# -------------------- Alert-Only
set profiles url-filtering Alert-Only-URL credential-enforcement mode ip-user
set profiles url-filtering Alert-Only-URL credential-enforcement log-severity medium
set profiles url-filtering Alert-Only-URL credential-enforcement alert [ Allow-URL-Geo-Exception Allow-URL-List Block-URL-List Custom-Decrypt Custom-No-Decrypt Custom-No-Decrypt-Expired "Palo Alto Networks - Microsoft 365 URL List" abortion abused-drugs adult alcohol-and-tobacco auctions business-and-economy command-and-control computer-and-internet-info content-delivery-networks copyright-infringement cryptocurrency dating dynamic-dns educational-institutions entertainment-and-arts extremism financial-services gambling games government grayware hacking health-and-medicine high-risk home-and-garden hunting-and-fishing insufficient-content internet-communications-and-telephony internet-portals job-search legal low-risk malware medium-risk military motor-vehicles music newly-registered-domain news not-resolved nudity online-storage-and-backup parked peer-to-peer personal-sites-and-blogs philosophy-and-political-advocacy phishing private-ip-addresses proxy-avoidance-and-anonymizers questionable ransomware real-estate real-time-detection recreation-and-hobbies reference-and-research religion search-engines sex-education shareware-and-freeware shopping social-networking society sports stock-advice-and-tools streaming-media swimsuits-and-intimate-apparel training-and-tools translation travel unknown weapons web-advertisements web-based-email web-hosting ]
set profiles url-filtering Alert-Only-URL mlav-engine-urlbased-enabled "Javascript Exploit Detection" mlav-policy-action alert
set profiles url-filtering Alert-Only-URL mlav-engine-urlbased-enabled "Phishing Detection" mlav-policy-action alert
set profiles url-filtering Alert-Only-URL alert [ Allow-URL-Geo-Exception Allow-URL-List Block-URL-List Custom-Decrypt Custom-No-Decrypt Custom-No-Decrypt-Expired "Palo Alto Networks - Microsoft 365 URL List" abortion abused-drugs adult alcohol-and-tobacco auctions business-and-economy command-and-control computer-and-internet-info content-delivery-networks copyright-infringement cryptocurrency dating dynamic-dns educational-institutions entertainment-and-arts extremism financial-services gambling games government grayware hacking health-and-medicine high-risk home-and-garden hunting-and-fishing insufficient-content internet-communications-and-telephony internet-portals job-search legal low-risk malware medium-risk military motor-vehicles music newly-registered-domain news not-resolved nudity online-storage-and-backup parked peer-to-peer personal-sites-and-blogs philosophy-and-political-advocacy phishing private-ip-addresses proxy-avoidance-and-anonymizers questionable ransomware real-estate real-time-detection recreation-and-hobbies reference-and-research religion search-engines sex-education shareware-and-freeware shopping social-networking society sports stock-advice-and-tools streaming-media swimsuits-and-intimate-apparel training-and-tools translation travel unknown weapons web-advertisements web-based-email web-hosting ]
# -------------------- Exceptions
set profiles url-filtering Exception-URL credential-enforcement mode ip-user
set profiles url-filtering Exception-URL credential-enforcement log-severity high
set profiles url-filtering Exception-URL credential-enforcement alert real-time-detection
set profiles url-filtering Exception-URL credential-enforcement block [ Allow-URL-List Block-URL-List abortion abused-drugs adult alcohol-and-tobacco auctions business-and-economy command-and-control computer-and-internet-info content-delivery-networks copyright-infringement cryptocurrency dating dynamic-dns educational-institutions entertainment-and-arts extremism financial-services gambling games government grayware hacking health-and-medicine high-risk home-and-garden hunting-and-fishing insufficient-content internet-communications-and-telephony internet-portals job-search legal low-risk malware medium-risk military motor-vehicles music newly-registered-domain news not-resolved nudity online-storage-and-backup parked peer-to-peer personal-sites-and-blogs philosophy-and-political-advocacy phishing private-ip-addresses proxy-avoidance-and-anonymizers questionable ransomware real-estate recreation-and-hobbies reference-and-research religion search-engines sex-education shareware-and-freeware shopping social-networking society sports stock-advice-and-tools streaming-media swimsuits-and-intimate-apparel training-and-tools translation travel unknown weapons web-advertisements web-based-email web-hosting ]
set profiles url-filtering Exception-URL log-http-hdr-user-agent yes
set profiles url-filtering Exception-URL log-http-hdr-referer yes
set profiles url-filtering Exception-URL log-http-hdr-xff yes
set profiles url-filtering Exception-URL mlav-engine-urlbased-enabled "Javascript Exploit Detection" mlav-policy-action block
set profiles url-filtering Exception-URL mlav-engine-urlbased-enabled "Phishing Detection" mlav-policy-action block
set profiles url-filtering Exception-URL alert [ Allow-URL-Geo-Exception Allow-URL-List Custom-Decrypt Custom-No-Decrypt Custom-No-Decrypt-Expired "Palo Alto Networks - Microsoft 365 URL List" abortion abused-drugs adult alcohol-and-tobacco auctions business-and-economy computer-and-internet-info content-delivery-networks copyright-infringement cryptocurrency dating dynamic-dns educational-institutions entertainment-and-arts extremism financial-services gambling games government hacking health-and-medicine high-risk home-and-garden hunting-and-fishing insufficient-content internet-communications-and-telephony internet-portals job-search legal low-risk medium-risk military motor-vehicles music newly-registered-domain news not-resolved nudity online-storage-and-backup parked peer-to-peer personal-sites-and-blogs philosophy-and-political-advocacy private-ip-addresses proxy-avoidance-and-anonymizers questionable real-estate real-time-detection recreation-and-hobbies reference-and-research religion search-engines sex-education shareware-and-freeware shopping social-networking society sports stock-advice-and-tools streaming-media swimsuits-and-intimate-apparel training-and-tools translation travel unknown weapons web-advertisements web-based-email web-hosting ]
set profiles url-filtering Exception-URL block [ Block-URL-List command-and-control grayware malware phishing ransomware ]
# -------------------- Outbound
set profiles url-filtering Outbound-URL credential-enforcement mode ip-user
set profiles url-filtering Outbound-URL credential-enforcement log-severity high
set profiles url-filtering Outbound-URL credential-enforcement alert [ Allow-URL-Geo-Exception Allow-URL-List Custom-Decrypt Custom-No-Decrypt Custom-No-Decrypt-Expired "Palo Alto Networks - Microsoft 365 URL List" alcohol-and-tobacco auctions business-and-economy computer-and-internet-info content-delivery-networks educational-institutions entertainment-and-arts financial-services government health-and-medicine home-and-garden hunting-and-fishing insufficient-content internet-communications-and-telephony internet-portals job-search legal low-risk medium-risk military motor-vehicles music news online-storage-and-backup personal-sites-and-blogs philosophy-and-political-advocacy private-ip-addresses real-estate real-time-detection recreation-and-hobbies reference-and-research religion search-engines shopping social-networking society sports stock-advice-and-tools streaming-media training-and-tools translation travel weapons web-advertisements web-based-email web-hosting ]
set profiles url-filtering Outbound-URL credential-enforcement block [ Block-URL-List abused-drugs adult command-and-control copyright-infringement cryptocurrency dating dynamic-dns extremism games grayware hacking high-risk malware not-resolved nudity peer-to-peer phishing proxy-avoidance-and-anonymizers questionable ransomware ]
set profiles url-filtering Outbound-URL credential-enforcement continue [ abortion gambling newly-registered-domain parked sex-education shareware-and-freeware swimsuits-and-intimate-apparel unknown ]
set profiles url-filtering Outbound-URL log-http-hdr-user-agent yes
set profiles url-filtering Outbound-URL log-http-hdr-referer yes
set profiles url-filtering Outbound-URL log-http-hdr-xff yes
set profiles url-filtering Outbound-URL mlav-engine-urlbased-enabled "Javascript Exploit Detection" mlav-policy-action block
set profiles url-filtering Outbound-URL mlav-engine-urlbased-enabled "Phishing Detection" mlav-policy-action block
set profiles url-filtering Outbound-URL alert [ Allow-URL-Geo-Exception Allow-URL-List Custom-Decrypt Custom-No-Decrypt Custom-No-Decrypt-Expired "Palo Alto Networks - Microsoft 365 URL List" alcohol-and-tobacco auctions business-and-economy computer-and-internet-info content-delivery-networks educational-institutions entertainment-and-arts financial-services government health-and-medicine home-and-garden hunting-and-fishing insufficient-content internet-communications-and-telephony internet-portals job-search legal low-risk medium-risk military motor-vehicles music news online-storage-and-backup personal-sites-and-blogs philosophy-and-political-advocacy private-ip-addresses real-estate real-time-detection recreation-and-hobbies reference-and-research religion search-engines shopping social-networking society sports stock-advice-and-tools streaming-media training-and-tools translation travel weapons web-advertisements web-based-email web-hosting ]
set profiles url-filtering Outbound-URL block [ Block-URL-List abused-drugs adult command-and-control copyright-infringement cryptocurrency dating dynamic-dns extremism games grayware hacking high-risk malware not-resolved nudity peer-to-peer phishing proxy-avoidance-and-anonymizers ransomware ]
set profiles url-filtering Outbound-URL continue [ abortion gambling newly-registered-domain parked questionable sex-education shareware-and-freeware swimsuits-and-intimate-apparel unknown ]
set profiles url-filtering Outbound-URL-IT credential-enforcement mode ip-user
set profiles url-filtering Outbound-URL-IT credential-enforcement log-severity high
set profiles url-filtering Outbound-URL-IT credential-enforcement alert [ Allow-URL-Geo-Exception Allow-URL-List Custom-Decrypt Custom-No-Decrypt Custom-No-Decrypt-Expired "Palo Alto Networks - Microsoft 365 URL List" alcohol-and-tobacco auctions business-and-economy computer-and-internet-info content-delivery-networks educational-institutions entertainment-and-arts financial-services games government health-and-medicine home-and-garden hunting-and-fishing insufficient-content internet-communications-and-telephony internet-portals job-search legal low-risk medium-risk military motor-vehicles music news online-storage-and-backup personal-sites-and-blogs philosophy-and-political-advocacy private-ip-addresses real-estate real-time-detection recreation-and-hobbies reference-and-research religion search-engines shopping social-networking society sports stock-advice-and-tools streaming-media training-and-tools translation travel weapons web-advertisements web-based-email web-hosting ]
set profiles url-filtering Outbound-URL-IT credential-enforcement block [ Block-URL-List abused-drugs adult command-and-control copyright-infringement cryptocurrency dating dynamic-dns extremism grayware hacking high-risk malware not-resolved nudity peer-to-peer phishing proxy-avoidance-and-anonymizers questionable ransomware ]
set profiles url-filtering Outbound-URL-IT credential-enforcement continue [ abortion gambling newly-registered-domain parked sex-education shareware-and-freeware swimsuits-and-intimate-apparel unknown ]
set profiles url-filtering Outbound-URL-IT log-http-hdr-user-agent yes
set profiles url-filtering Outbound-URL-IT log-http-hdr-referer yes
set profiles url-filtering Outbound-URL-IT log-http-hdr-xff yes
set profiles url-filtering Outbound-URL-IT mlav-engine-urlbased-enabled "Javascript Exploit Detection" mlav-policy-action block
set profiles url-filtering Outbound-URL-IT mlav-engine-urlbased-enabled "Phishing Detection" mlav-policy-action block
set profiles url-filtering Outbound-URL-IT description "Outbound URL filter for IT users only"
set profiles url-filtering Outbound-URL-IT alert [ Allow-URL-Geo-Exception Allow-URL-List Custom-Decrypt Custom-No-Decrypt Custom-No-Decrypt-Expired "Palo Alto Networks - Microsoft 365 URL List" alcohol-and-tobacco auctions business-and-economy computer-and-internet-info content-delivery-networks educational-institutions entertainment-and-arts financial-services games government health-and-medicine home-and-garden hunting-and-fishing insufficient-content internet-communications-and-telephony internet-portals job-search legal low-risk medium-risk military motor-vehicles music news online-storage-and-backup personal-sites-and-blogs philosophy-and-political-advocacy private-ip-addresses real-estate real-time-detection recreation-and-hobbies reference-and-research religion search-engines shareware-and-freeware shopping social-networking society sports stock-advice-and-tools streaming-media training-and-tools translation travel weapons web-advertisements web-based-email web-hosting ]
set profiles url-filtering Outbound-URL-IT block [ Block-URL-List abused-drugs adult command-and-control copyright-infringement cryptocurrency dating dynamic-dns extremism grayware hacking high-risk malware not-resolved nudity peer-to-peer phishing proxy-avoidance-and-anonymizers ransomware ]
set profiles url-filtering Outbound-URL-IT continue [ abortion gambling newly-registered-domain parked questionable sex-education swimsuits-and-intimate-apparel unknown ]
```
## Wildfire
---
```bash
# -------------------- Outbound
set profiles wildfire-analysis Outbound-WF rules Forward-All application any
set profiles wildfire-analysis Outbound-WF rules Forward-All direction both
set profiles wildfire-analysis Outbound-WF rules Forward-All analysis public-cloud
set profiles wildfire-analysis Outbound-WF rules Forward-All file-type any
# -------------------- Inbound
set profiles wildfire-analysis Inbound-WF rules Forward-All application any
set profiles wildfire-analysis Inbound-WF rules Forward-All direction both
set profiles wildfire-analysis Inbound-WF rules Forward-All analysis public-cloud
set profiles wildfire-analysis Inbound-WF rules Forward-All file-type any
# -------------------- Internal
set profiles wildfire-analysis Internal-WF rules Forward-All application any
set profiles wildfire-analysis Internal-WF rules Forward-All direction both
set profiles wildfire-analysis Internal-WF rules Forward-All analysis public-cloud
set profiles wildfire-analysis Internal-WF rules Forward-All file-type any
# -------------------- Alert-Only
set profiles wildfire-analysis Alert-Only-WF rules Forward-All application any
set profiles wildfire-analysis Alert-Only-WF rules Forward-All direction both
set profiles wildfire-analysis Alert-Only-WF rules Forward-All analysis public-cloud
set profiles wildfire-analysis Alert-Only-WF rules Forward-All file-type any
```

## Security Profile Groups
---
```bash
# -------------------- Outbound
set profile-group Outbound-SPG virus Outbound-AV
set profile-group Outbound-SPG spyware Outbound-AS
set profile-group Outbound-SPG vulnerability Outbound-VP
set profile-group Outbound-SPG url-filtering Outbound-URL
set profile-group Outbound-SPG file-blocking Outbound-FB
set profile-group Outbound-SPG wildfire-analysis Outbound-WF
# -------------------- Inbound
set profile-group Inbound-SPG virus Inbound-AV
set profile-group Inbound-SPG spyware Inbound-AS
set profile-group Inbound-SPG vulnerability Inbound-VP
set profile-group Inbound-SPG file-blocking Inbound-FB
set profile-group Inbound-SPG wildfire-analysis Inbound-WF
# -------------------- Internal
set profile-group Internal-SPG virus Internal-AV
set profile-group Internal-SPG spyware Internal-AS
set profile-group Internal-SPG vulnerability Internal-VP
set profile-group Internal-SPG file-blocking Internal-FB
set profile-group Internal-SPG wildfire-analysis Internal-WF
# -------------------- Alert-Only
set profile-group Alert-Only-SPG virus Alert-Only-AV
set profile-group Alert-Only-SPG spyware Alert-Only-AS
set profile-group Alert-Only-SPG vulnerability Alert-Only-VP
set profile-group Alert-Only-SPG url-filtering Alert-Only-URL
set profile-group Alert-Only-SPG file-blocking Alert-Only-FB
set profile-group Alert-Only-SPG wildfire-analysis Alert-Only-WF
# -------------------- Default
set profile-group default-SPG virus Outbound-AV
set profile-group default-SPG spyware Outbound-AS
set profile-group default-SPG vulnerability Outbound-VP
set profile-group default-SPG url-filtering Outbound-URL
set profile-group default-SPG file-blocking Outbound-FB
set profile-group default-SPG wildfire-analysis Outbound-WF
```

## Network Profiles
---
```bash
# -------------------- Zone Protection
set network profiles zone-protection-profile Recommended_Zone_Protection flood tcp-syn enable yes
set network profiles zone-protection-profile Recommended_Zone_Protection flood tcp-syn syn-cookies alarm-rate 10000
set network profiles zone-protection-profile Recommended_Zone_Protection flood tcp-syn syn-cookies activate-rate 10000
set network profiles zone-protection-profile Recommended_Zone_Protection flood tcp-syn syn-cookies maximal-rate 46800
set network profiles zone-protection-profile Recommended_Zone_Protection flood icmp red alarm-rate 10000
set network profiles zone-protection-profile Recommended_Zone_Protection flood icmp red activate-rate 10000
set network profiles zone-protection-profile Recommended_Zone_Protection flood icmp red maximal-rate 46800
set network profiles zone-protection-profile Recommended_Zone_Protection flood icmp enable yes
set network profiles zone-protection-profile Recommended_Zone_Protection flood icmpv6 red alarm-rate 10000
set network profiles zone-protection-profile Recommended_Zone_Protection flood icmpv6 red activate-rate 10000
set network profiles zone-protection-profile Recommended_Zone_Protection flood icmpv6 red maximal-rate 46800
set network profiles zone-protection-profile Recommended_Zone_Protection flood icmpv6 enable yes
set network profiles zone-protection-profile Recommended_Zone_Protection flood other-ip red alarm-rate 10000
set network profiles zone-protection-profile Recommended_Zone_Protection flood other-ip red activate-rate 10000
set network profiles zone-protection-profile Recommended_Zone_Protection flood other-ip red maximal-rate 46800
set network profiles zone-protection-profile Recommended_Zone_Protection flood other-ip enable yes
set network profiles zone-protection-profile Recommended_Zone_Protection flood udp red alarm-rate 10000
set network profiles zone-protection-profile Recommended_Zone_Protection flood udp red activate-rate 10000
set network profiles zone-protection-profile Recommended_Zone_Protection flood udp red maximal-rate 46800
set network profiles zone-protection-profile Recommended_Zone_Protection flood udp enable yes
set network profiles zone-protection-profile Recommended_Zone_Protection scan 8001 action block
set network profiles zone-protection-profile Recommended_Zone_Protection scan 8001 interval 2
set network profiles zone-protection-profile Recommended_Zone_Protection scan 8001 threshold 100
set network profiles zone-protection-profile Recommended_Zone_Protection scan 8002 action block
set network profiles zone-protection-profile Recommended_Zone_Protection scan 8002 interval 10
set network profiles zone-protection-profile Recommended_Zone_Protection scan 8002 threshold 100
set network profiles zone-protection-profile Recommended_Zone_Protection scan 8003 action block
set network profiles zone-protection-profile Recommended_Zone_Protection scan 8003 interval 2
set network profiles zone-protection-profile Recommended_Zone_Protection scan 8003 threshold 100
set network profiles zone-protection-profile Recommended_Zone_Protection discard-ip-spoof yes
set network profiles zone-protection-profile Recommended_Zone_Protection discard-malformed-option yes
set network profiles zone-protection-profile Recommended_Zone_Protection remove-tcp-timestamp yes
set network profiles zone-protection-profile Recommended_Zone_Protection strip-tcp-fast-open-and-data no
set network profiles zone-protection-profile Recommended_Zone_Protection strip-mptcp-option global
set network profiles zone-protection-profile Recommended_Zone_Protection discard-unknown-option yes
set network profiles zone-protection-profile Recommended_Zone_Protection scan-white-list RFC1918-10.0.0.0-8 ipv4 10.0.0.0/8
```
## System Config
---
```bash
# -------------------- Various system configuration bits
set deviceconfig system device-telemetry device-health-performance yes
set deviceconfig system device-telemetry product-usage yes
set deviceconfig system device-telemetry threat-prevention yes
set deviceconfig system device-telemetry region Americas

set deviceconfig system update-schedule anti-virus recurring hourly action download-and-install
set deviceconfig system update-schedule anti-virus recurring sync-to-peer yes
set deviceconfig system update-schedule threats recurring every-30-mins at 0
set deviceconfig system update-schedule threats recurring every-30-mins action download-and-install
set deviceconfig system update-schedule threats recurring threshold 24
set deviceconfig system update-schedule threats recurring new-app-threshold 72
set deviceconfig system update-schedule threats recurring sync-to-peer yes
set deviceconfig system update-schedule wildfire recurring every-min action download-and-install
set deviceconfig system update-schedule wildfire recurring every-min sync-to-peer yes

set mgt-config password-complexity enabled yes
set mgt-config password-complexity minimum-length 12
set mgt-config password-complexity minimum-uppercase-letters 1
set mgt-config password-complexity minimum-lowercase-letters 1
set mgt-config password-complexity minimum-numeric-letters 1
set mgt-config password-complexity minimum-special-characters 1
set mgt-config password-complexity block-username-inclusion yes
set mgt-config password-complexity password-history-count 24
set mgt-config password-complexity new-password-differs-by-characters 3
```
## Log settings
---  

```bash
# -------------------- Default Logging setting
set shared log-settings syslog Sample_Syslog_Profile server Sample_Syslog transport UDP
set shared log-settings syslog Sample_Syslog_Profile server Sample_Syslog port 514
set shared log-settings syslog Sample_Syslog_Profile server Sample_Syslog format BSD
set shared log-settings syslog Sample_Syslog_Profile server Sample_Syslog server 192.0.2.2
set shared log-settings syslog Sample_Syslog_Profile server Sample_Syslog facility LOG_USER

set shared log-settings system match-list System_Log_Forwarding filter "All Logs"
set shared log-settings system match-list System_Log_Forwarding send-to-panorama no
set shared log-settings system match-list System_Log_Forwarding send-syslog
set shared log-settings system match-list Email_Critical_System_Logs send-email Email_Server_Profile
set shared log-settings system match-list Email_Critical_System_Logs filter "(severity eq critical) and ( subtype neq device-telemetry )"
set shared log-settings system match-list Email_Critical_System_Logs description "Email Critical System Logs"
set shared log-settings config match-list
set shared log-settings userid match-list User-ID_Log_Forwarding filter "All Logs"
set shared log-settings userid match-list User-ID_Log_Forwarding send-to-panorama no
set shared log-settings hipmatch match-list HIP_Log_Forwarding filter "All Logs"
set shared log-settings hipmatch match-list HIP_Log_Forwarding send-to-panorama no
set shared log-settings globalprotect match-list GP_Log_Forwarding filter "All Logs"
set shared log-settings globalprotect match-list GP_Log_Forwarding send-to-panorama no
set shared log-settings iptag match-list IP-Tag_Log_Forwarding filter "All Logs"
set shared log-settings iptag match-list IP-Tag_Log_Forwarding send-to-panorama no

set shared log-settings email Email_Server_Profile server SMTP_Server_Profile display-name Threat_Alerts
set shared log-settings email Email_Server_Profile server SMTP_Server_Profile gateway mail.fbmsales.com
set shared log-settings email Email_Server_Profile server SMTP_Server_Profile protocol SMTP
set shared log-settings email Email_Server_Profile server SMTP_Server_Profile port 25
set shared log-settings email Email_Server_Profile server SMTP_Server_Profile tls-version 1.2
set shared log-settings email Email_Server_Profile server SMTP_Server_Profile from azure-firewall@fbmsales.com
set shared log-settings email Email_Server_Profile server SMTP_Server_Profile to network@fbmsales.com
```


-eof-