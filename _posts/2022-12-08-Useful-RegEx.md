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


```
[ \t]+$
```

## Select everything between two words

```
(.*)
```

## Remove all blank lines

```
^(?:[\t ]*(?:\r?\n|\r))+
```

> Example: Lets remove the blank lines from this config

```
config firewall address
edit "*.evercast-webrtc.com"

set type fqdn
set fqdn "*.evercast-webrtc.com"
next
```

> Open Search and Replace and type in the RegEx above and the results will be
```
config firewall address
edit "*.evercast-webrtc.com"
set type fqdn
set fqdn "*.evercast-webrtc.com"
next
```
## Select everything up until specified character

```
^[^$]*
```


-eof-