---
title: "Troubleshooting Fortinet"
date: 2023-03-17 10:00:00 -700
categories: [Fortinet]
tags: [troubleshooting, cmd, firewall, network, fortinet]    #TAG names should always be lowercase
---

# Troubleshooting Fortnet Firewalls

These are some of the commands I have used to troubleshoot on Fortinet FortiGate (and FortiWifi) firewalls

## CLI commands

We'll start out with some basic CLI command sets

### Basics

```shell
get sys status              # Show status summary
get sys perf stat           # Show Fortigate ressources summary
exec shutdown/reboot        # Shutdown the device/reboot
execute ping(-options)      # Ping something (can add options)
execute ssh <user>@<ip>     # SSH to another server
get sys arp                 # Show the arp table
show | grep -f something    #  Find where “something” is used (cases-sensitive, can use -i
to be case insensitive)
```

### Config Management

```shell
diag hard deviceinfo disk   # Show disks and partitions usage
diag sys flash list         # Show partitions status
exec factoryreset           # Reset to factory default
exec restore config         # Restore configuration (reboots)
exec backup conf            # Backup configuration
exec formatlogdisk          # Format log disk
get sys ha status           # Display HA conf summary
diag sys ha history read    # Display HA history events
exec ha synchronize all     # Synchronize all parts of the confi
```

### Interface Commands

```shell
show/get system interface   # Show interfaces status. Use get to retrieve dynamic information
(such as PPPoE IP)
config sys interface        # Basic interface ip configuration
 edit <port>
 set ip x.x.x.x/y
 set allow ssh ping https
end

diag hard dev nic <port>    # Show interfaces statistics
diag netlink device list    # Show interfaces statistics (errors)
```

### VPN Commands

```shell
diag vpn ike gateway list   # Show phase 1
diag vpn tunnel list        # Show phase 2
diag vpn ike gateway flush name <phase1> # Flush phase 1
diag vpn tunnel up <phase2> # Bring up a phase 2

diag debug en               # Troubleshoot VPN issue
diag vpn ike log-filter daddr x.x.x.x
diag debug app ike 1
```

### Routing Commands

```shell
config router static            # Add a static route
 edit 0
 set device internal
 set dst x.x.x.x/y
 set gateway z.z.z.z
end

get ro info ro details x.x.x.x  # Display the route used
diag firewall proute list       # Display the Policy Routes
get router info routingtable all    # Display the current routing table active/configured
diag ip route list              # Display the kernel routing table
```

## Flow Tracing

One of my favorite things to use is Flow Tracing. It lets you see what happens to the packets as they ingress and/or egress the firewall

```shell
diag debug disable
diag debug reset
diag debug flow filter addr 10.2.10.20
diag debug flow trace start 5
diag debug console timestamp enable
diag debug enable
```

You can modify some of these parameters, but this is a baseline I start with

## Packet Capture

```shell
diag debug disable
diag debug reset
diag debug packet any 'host <IP>' 4 0
```

## Debug SSLVPN

```shell
diag debug application sslvpn -1
```

## Debug FortiGate non-blocking AUTH daemon

To debug authentication issues

```shell
diag debug disable
diag debug reset
diag debug application fnbamd -1
```
## Log into Secondary firewall, in an HA cluster

During a recent firmware upgrade, I ran into a problem and needed to log into the secondary firewall to kick off the upgrade process. We didn't have seperate/out-of-band management interfaces, so this is how I did it.

```shell
# On primary FW, the following command lists the firewalls in the cluster
execute ha manage ?

# Then just do this, 0 being the ID of the secondary firewall
execute ha manager 0 admin  <-- Replace with your username
```

That will log you into the secondary FW and allow you to run commands on it.

## Useful Links

These are some useful links, which I've used in the past to help troubleshoot various FortiGate issues

[Troubleshooting HA synchronization issues](https://community.fortinet.com/t5/FortiGate/Troubleshooting-Tip-HA-synchronization-issue-cluster-out-of-sync/ta-p/193422?externalID=FD45183)

[Procedure for HA manual synchronization](https://community.fortinet.com/t5/FortiGate/Technical-Tip-Procedure-for-HA-manual-synchronization/ta-p/196067?externalID=FD36494)

[Fix an HA (High Availability) cluster upgrade failure](https://community.fortinet.com/t5/FortiGate/Troubleshooting-Tip-Fix-an-HA-High-Availability-cluster-upgrade/ta-p/242194)



-eof-