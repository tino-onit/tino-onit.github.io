---
title: "Network Snippets"
date: 2022-09-19 15:00:00 -700
categories: [Troubleshooting]
tags: [network, powershell, linux, cmd, snippet]    #TAG names should always be lowercase
---

# Network Snippets

Various snippets of code or one liners that I use often. Stuff that helps in day-2-day troubleshooting and whatnot.

---

## Linux Commands

| What | CMD | AltCMD |
|----|----|----|
| Ping Default GW | Ping | Ping |
| Verify IP | ifconfig -a | ip a |
| Verify Link Up | ifconfig -a | ip a |
| Verify Default GW | netstat -rn | ip route |
| Verify Route Table | netstat -rn | ip route |
| Verify ARP Table | arp -an | ip neighbor |

## Windows Commands

| What | CMD | |
|----|----|----|
| Ping Default GW | Ping | |
| Verify IP | ipconfig | |
| Verify Link Up | ipconfig | |
| Verify Default GW | netstat -rn | |
| Verify Route Table | netstat -rn | |
| Verify ARP Table | arp -a | |

## Linux cmd to get public IP

```console
curl ifconfig.io/ip
```

## Linux cmd to get public IP continuously

```bash
while true; do curl ifconfig.io/ip; done
```

## PowerShell cmd to get public IP and Country

```powershell
curl ifconfig.io/ip | Out-String -Stream| Select-String "Content"
curl ifconfig.io/country_code | Out-String -Stream| Select-String "Content"
```


## Cisco IOS command to show Assigned and UP interfaces

```console
show ip int br | e unassigned|administratively
```
## SSH Command to connect to older devices

If you've ever received the following message (or similar)

```bash
Unable to negotiate with 10.2.0.16 port 22: no matching key exchange method found. Their offer: diffie-hellman-group-exchange-sha1,diffie-hellman-group14-sha1
```

What you can do is tell SSH to use a (less secure) different exchange method

```bash
ssh -o KexAlgorithms=

curve25519-sha256                       diffie-hellman-group18-sha512           ecdh-sha2-nistp384
curve25519-sha256@libssh.org            diffie-hellman-group1-sha1              ecdh-sha2-nistp521
diffie-hellman-group14-sha1             diffie-hellman-group-exchange-sha1      sntrup4591761x25519-sha512@tinyssh.org
diffie-hellman-group14-sha256           diffie-hellman-group-exchange-sha256    
diffie-hellman-group16-sha512           ecdh-sha2-nistp256     
```

So then something like this will get you connected to the device via SSH

```bash
ssh -o KexAlgorithms=diffie-hellman-group14-sha1 admin@10.2.0.16
```

## Counting the lines of Cisco IOS output

If you wanted to count the lines that a command outputs, you could do the following. For example, you want to count how many interfaces are connected.
First thing that comes to mind is something like

```console
SW-1#show int | inc connected  
GigabitEthernet1/0/2 is up, line protocol is up (connected) 
GigabitEthernet1/0/3 is up, line protocol is up (connected) 
GigabitEthernet1/0/5 is up, line protocol is up (connected) 
... 
GigabitEthernet2/0/49 is up, line protocol is up (connected) 
Port-channel1 is up, line protocol is up (connected) 
```

Which is nice, but if you just want a simple number then you can do the following

```console
SW-1#show int | count connected      
Number of lines which match regexp = 41
```
## Cleanup files on Windows

Not really a network thing, but useful none the less. Cleanup files on your Windows workstation

```console
1.) Clear %TEMP%
2.) Clear C:\Windows\Temp
3.) Run cleanmgr.exe
4.) Run patchmypc.exe
5.) Delete stuff in %APPDATA% you don't have anymore
```

---

# Working with Git

Clone existing repo (remote, GitHub) to local server

```console
git clone git@<YOUR-USER-NAME>/<YOUR-REPO-NAME>.git
```

## Push local repo changes to remote GitHub repo

```console
git add .
git commit -m "made some changes"
git push
```

## Sync or Merge remote repo GitHub repo changes back to local repo

```console
git pull
```

-eof-