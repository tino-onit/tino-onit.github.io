---
title: "Using NMAP"
date: 2024-02-10 10:00:00 -700
categories: [misc]
tags: [network,snippet,cmd,troubleshooting,bash]    #TAG names should always be lowercase
---

# Using NMAP

Scan a single host or an IP address (IPv4)

```bash
# Scan a single ip address
nmap 192.168.1.1
# Scan a host name
nmap server1.cyberciti.biz
# Scan a host name with more info
nmap -v server1.cyberciti.biz
```

Scan multiple IP address or subnet (IPv4)

```bash
nmap 192.168.1.1 192.168.1.2 192.168.1.3
# works with same subnet i.e. 192.168.1.0/24
nmap 192.168.1.1,2,3
```

You can scan a range of IP address too:

```bash
nmap 192.168.1.1-20
```

You can scan a range of IP address using a wildcard:

```bash
nmap 192.168.1.*
```

Finally, you scan an entire subnet:

```bash
nmap 192.168.1.0/24
```

## Read list of hosts/networks from a file (IPv4)

The -iL option allows you to read the list of target systems using a text file. This is useful to scan a large number of hosts/networks. Create a text file as follows:
cat > /tmp/test.txt
Sample outputs:

```bash
server1.cyberciti.biz
192.168.1.0/24
192.168.1.1/24
10.1.2.3
localhost
```

The syntax is:

```bash
nmap -iL /tmp/test.txt
```

## Excluding hosts/networks (IPv4)

When scanning a large number of hosts/networks you can exclude hosts from a scan:

```bash
nmap 192.168.1.0/24 --exclude 192.168.1.5
nmap 192.168.1.0/24 --exclude 192.168.1.5,192.168.1.254
```

OR exclude list from a file called /tmp/exclude.txt

```bash
nmap -iL /tmp/scanlist.txt --excludefile /tmp/exclude.txt
```

## Turn on OS and version detection scanning script (IPv4)

```bash
nmap -A 192.168.1.254
nmap -v -A 192.168.1.1
nmap -A -iL /tmp/scanlist.txt 
```

## Find out if a host/network is protected by a firewall

```bash
nmap -sA 192.168.1.254
nmap -sA server1.cyberciti.biz
```

## Scan a host when protected by the firewall

```bash
nmap -PN 192.168.1.1
nmap -PN server1.cyberciti.biz
```

## Scan an IPv6 host/address

The -6 option enable IPv6 scanning. The syntax is:

```bash
nmap -6 IPv6-Address-Here
nmap -6 server1.cyberciti.biz
nmap -6 2607:f0d0:1002:51::4
nmap -v A -6 2607:f0d0:1002:51::4
```

## Scan a network and find out which servers and devices are up and running

This is known as host discovery or ping scan:

```bash
nmap -sP 192.168.1.0/24
```

## How do I perform a fast scan?

```bash
nmap -F 192.168.1.1
```

## Display the reason a port is in a particular state

```bash
nmap --reason 192.168.1.1
nmap --reason server1.cyberciti.biz
```

## Only show open (or possibly open) ports

```bash
nmap --open 192.168.1.1
nmap --open server1.cyberciti.biz
```

## Show all packets sent and received

```bash
nmap --packet-trace 192.168.1.1
nmap --packet-trace server1.cyberciti.biz
```

## Show host interfaces and routes

This is useful for debugging (ip command or route command or netstat command like output using nmap)

```bash
nmap --iflist
```

## How do I scan specific ports?

```bash
map -p [port] hostName
# Scan port 80
nmap -p 80 192.168.1.1
 
# Scan TCP port 80
nmap -p T:80 192.168.1.1
 
# Scan UDP port 53
nmap -p U:53 192.168.1.1
 
# Scan two ports
nmap -p 80,443 192.168.1.1
 
# Scan port ranges
nmap -p 80-200 192.168.1.1
 
# Combine all options
nmap -p U:53,111,137,T:21-25,80,139,8080 192.168.1.1
nmap -p U:53,111,137,T:21-25,80,139,8080 server1.cyberciti.biz
nmap -v -sU -sT -p U:53,111,137,T:21-25,80,139,8080 192.168.1.254
 
# Scan all ports with * wildcard
nmap -p "*" 192.168.1.1
 
# Scan top ports i.e. scan $number most common ports
nmap --top-ports 5 192.168.1.1
nmap --top-ports 10 192.168.1.1
```

## The fastest way to scan all your devices/computers for open ports ever

```bash
nmap -T5 192.168.1.0/24
```

## How do I detect remote operating system?

You can identify a remote host apps and OS using the -O option:

```bash
nmap -O 192.168.1.1
nmap -O  --osscan-guess 192.168.1.1
nmap -v -O --osscan-guess 192.168.1.1

```

## How do I detect remote services (server / daemon) version numbers?

```bash
nmap -sV 192.168.1.1
```

Sample outputs:

```bash
Starting Nmap 5.00 ( http://nmap.org ) at 2012-11-27 01:34 IST
Interesting ports on 192.168.1.1:
Not shown: 998 closed ports
PORT   STATE SERVICE VERSION
22/tcp open  ssh     Dropbear sshd 0.52 (protocol 2.0)
80/tcp open  http?
1 service unrecognized despite returning data.

```

## Scan a host using TCP ACK (PA) and TCP Syn (PS) ping

If firewall is blocking standard ICMP pings, try the following host discovery methods:

```bash
nmap -PS 192.168.1.1
nmap -PS 80,21,443 192.168.1.1
nmap -PA 192.168.1.1
nmap -PA 80,21,200-512 192.168.1.1
```

## Scan a host using IP protocol ping

```bash
nmap -PO 192.168.1.1
```

## Scan a host using UDP ping

This scan bypasses firewalls and filters that only screen TCP:

```bash
nmap -PU 192.168.1.1
nmap -PU 2000.2001 192.168.1.1
```

## Find out the most commonly used TCP ports using TCP SYN Scan

```bash
## Stealthy scan#
nmap -sS 192.168.1.1
 
## Find out the most commonly used TCP ports using  TCP connect scan (warning: no stealth scan)
##  OS Fingerprinting#
nmap -sT 192.168.1.1
 
## Find out the most commonly used TCP ports using TCP ACK scan
nmap -sA 192.168.1.1
 
## Find out the most commonly used TCP ports using TCP Window scan
nmap -sW 192.168.1.1
 
## Find out the most commonly used TCP ports using TCP Maimon scan
nmap -sM 192.168.1.1
 
```

## Scan a host for UDP services (UDP scan)

Most popular services on the Internet run over the TCP protocol. DNS, SNMP, and DHCP are three of the most common UDP services. Use the following syntax to find out UDP services:

```bash
nmap -sU nas03
nmap -sU 192.168.1.1
```

## Scan a firewall for security weakness

The following scan types exploit a subtle loophole in the TCP and good for testing security of common attacks:

```bash
# TCP Null Scan to fool a firewall to generate a response
# Does not set any bits (TCP flag header is 0)
nmap -sN 192.168.1.254
 
# TCP Fin scan to check firewall
# Sets just the TCP FIN bit
nmap -sF 192.168.1.254
 
# TCP Xmas scan to check firewall
# Sets the FIN, PSH, and URG flags, lighting the packet up like a Christmas tree
nmap -sX 192.168.1.254
 
```

See how to block Xmas packets, syn-floods and other conman attacks with iptables.

## Scan a firewall for packets fragments

The -f option causes the requested scan (including ping scans) to use tiny fragmented IP packets. The idea is to split up the TCP header over
several packets to make it harder for packet filters, intrusion detection systems, and other annoyances to detect what you are doing.

```bash
nmap -f 192.168.1.1
nmap -f fw2.nixcraft.net.in
nmap -f 15 fw2.nixcraft.net.in
# Set your own offset size with the --mtu option
nmap --mtu 32 192.168.1.1

```

## Cloak a scan with decoys

The -D option it appear to the remote host that the host(s) you specify as decoys are scanning the target network too. Thus their IDS might report 5-10 port scans from unique IP addresses, but they won't know which IP was scanning them and which were innocent decoys:

```bash
nmap -n -Ddecoy-ip1,decoy-ip2,your-own-ip,decoy-ip3,decoy-ip4 remote-host-ip
nmap -n -D192.168.1.5,10.5.1.2,172.1.2.4,3.4.2.1 192.168.1.5
```

## Scan a firewall for MAC address spoofing

```bash
## Spoof your MAC address
nmap --spoof-mac MAC-ADDRESS-HERE 192.168.1.1
 
## Add other options#
nmap -v -sT -PN --spoof-mac MAC-ADDRESS-HERE 192.168.1.1
 
 
## Use a random MAC address#
## The number 0, means nmap chooses a completely random MAC address#
nmap -v -sT -PN --spoof-mac 0 192.168.1.1
 
```

## How do I save output to a text file?

The syntax is:

```bash
nmap 192.168.1.1 > output.txt
nmap -oN /path/to/filename 192.168.1.1
nmap -oN output.txt 192.168.1.1
```


-eof-