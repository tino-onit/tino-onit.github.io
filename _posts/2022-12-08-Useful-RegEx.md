---
title: "Useful RegEx Snippets"
date: 2022-12-08 10:00:00 -700
categories: [Misc]
tags: [network, regex, cmd, snippet]    #TAG names should always be lowercase
---

# RegEx Snippets

These are some useful RegEx snippets that I find myself using over and over. This is a list that will be updated often

---

### Selecting all the trailing whitespace (tabs or space)

```shell
[ \t]+$
```

### Select everything between two words

```shell
(.*)
```

### Remove all blank lines

```shell
^(?:[\t ]*(?:\r?\n|\r))+
```

> Example: Lets remove the blank lines from this config

```shell
config firewall address
edit "*.evercast-webrtc.com"

set type fqdn
set fqdn "*.evercast-webrtc.com"
next
```

> Open Search and Replace and type in the RegEx above and the results will be

```shell
config firewall address
edit "*.evercast-webrtc.com"
set type fqdn
set fqdn "*.evercast-webrtc.com"
next
```

### Select everything up until specified character

```shell
^[^$]*
```

### Select everything, from a character/word/other, to the end. Including blank spaces

```shell
([ ,]+)(connected)(.*)
```

> Example: Lets remove everything except the interface name

```shell
# Cisco switchport
Gi1/0/30                        connected    107        a-full a-1000 10/100/1000BaseTX
```

> Open Search and Replace and type in the RegEx above and the results will be

```shell
Gi1/0/30
```

### Select all characters up to a specific character

If you need to select everything up until a specific character, you can use the following

> Example: 160117.070: CMD: 'show stackwise-virtual links' 16:44:44 PDT Mon Sep 11 2023

```shell
160([^:]*):
```

Will select _160117.070:_ up until the first ":"

### IPv4 Addresses

Found myself needing to parse some logs for IPv4 addresses recently and a colleague helped me out and send this over my way.

> Example: You want to filter out IPv4 addresses that start with 192.168.x.x

```shell
# all 4 octets
# (\d{1,3}\.\d{1,3}\.\d{1,3}\.\d{1,3})
192.168.(\d{1,3}\.\d{1,3})
```

-eof-