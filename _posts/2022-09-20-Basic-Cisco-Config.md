---
title: "Basic Cisco Router/Switch Config"
date: 2022-09-20 10:00:00 -700
categories: [snippet]
tags: [network, cmd]    #TAG names should always be lowercase
---

# Configuring Cisco devices
#### Below are is what I like to typically configure on a new Cisco router or switch. Nothing really specific, just some nice CLI changes, logging, and remote access items.
---
### Change *HOSTNAME*, *INTERFACE*, and *IP ADDRESS* as needed

```xml
hostname <HOSTNAME>
!
ip domain name <DOMAIN_NAME>
ip cef
!
no ip domain lookup
!
username admin password 0 <PASSWORD>
enable secret <PASSWORD>
!
crypto key generate rsa modulus 2048
ip ssh version 2
!
line vty 0 4
 login local
 transport input ssh
 logging synchronous
!
line con 0
 login local
 logging synchronous
!
logging buffered 512000 informational
!
archive
 log config
  logging enable
  logging size 200
  hidekeys
!
end
```
{: .nolineno }

### If needed, to disable the "phone home" feature
```xml
!
grep -q -F '127.0.0.1 xml.cisco.com' /etc/hosts || echo '127.0.0.1 xml.cisco.com' | sudo tee -a /etc/hosts"
!
```
{: .nolineno }
The command adds the following text to /etc/hosts:
"127.0.0.1 xml.cisco.com"


### If that doesn't work try
```xml
!
term shell
echo '127.0.0.127 xml.cisco.com' >> /etc/hosts
!
```
{: .nolineno }

### Additional
This has worked well for me, as a baseline to getting an switch or router configured