---
title: "Useful RegEx Snippets"
date: 2022-12-08 10:00:00 -700
categories: [Misc]
tags: [network, regex, cmd, snippet]    #TAG names should always be lowercase
---

# RegEx Snippets

These are some useful RegEx snippets that I find myself using over and over. This is a list that will be updated often

---

## Selecting all the trailing whitespace (tabs or space)

```shell
[ \t]+$
```

## Select everything between two words

```shell
(.*)
```

## Remove all blank lines

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

## Select everything up until specified character

```shell
^[^$]*
```

## Select everything, from a charector/word/other, to the end. Including blank spaces

```shell
([ ,]+)(M)(.*)
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

-eof-