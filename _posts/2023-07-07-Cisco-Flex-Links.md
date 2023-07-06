---
title: "Cisco Flex Links"
date: 2023-07-07 10:00:00 -700
categories: [Cisco]
tags: [cisco, config, cmd, network]    #TAG names should always be lowercase
---

# Cisco Flex Links

I recently needed to configure P2P link redundancy for an offsite location, where we had an AT&T (Primary) P2P circuit and a LoS microwave (Backup) link. In addition, the infrastructure at this site consisted of only two Cisco C2960X switch in a stack configuration.

The lack of a router or firewall seemed like it would hinder our ability to have redundant links, but then I found the following feature on the switching platform.

From [Cisco](https://www.cisco.com/c/en/us/td/docs/switches/lan/catalyst3550/software/release/12-2_25_see/configuration/guide/swflink.html)
> Flex Links are a pair of a Layer 2 interfaces (switch ports or port channels) where one interface is configured to act as a backup to the other. The feature provides an alternative solution to the Spanning Tree Protocol (STP). Users can disable STP and still retain basic link redundancy.

In addition
> You configure Flex Links on one Layer 2 interface (the active link) by assigning another Layer 2 interface as the Flex Link or backup link. The Flex Link can be on the same switch. When one of the links is up and forwarding traffic, the other link is in standby mode, ready to begin forwarding traffic if the other link shuts down. At any given time, only one of the interfaces is in the linkup state and forwarding traffic. If the primary link shuts down, the standby link starts forwarding traffic. When the active link comes back up, it goes into standby mode and does not forward traffic. STP is disabled on Flex Link interfaces.

## Topology

![Topology Image](https://i.imgur.com/1zit39b.png)

As you see, there are no L3 devices in this topology. Also, we don't want to load-balance - which is an option of Flex Links - we just want an Active (AT&T) and Passive (Microwave) setup.

## Configuration

The config on the main building side of things is easy, just configure the ports as trunk ports.

```shell
# Aruba Switch 1 - AT&T Circuit
aruba-sw01#
interface 1/45
   name "P2P-to-Warehouse-via-ATT-CID12345"
   tagged vlan 105-107,400-401
   untagged vlan 1
   spanning-tree root-guard
   exit
!
# Aruba Switch 2 - microwave link
aruba-sw02#
interface 1/45
   name "P2P-Metro-to-Warehouse"
   tagged vlan 105-107,400-401
   untagged vlan 1
   spanning-tree root-guard
   exit
```

On the Cisco side, that's where we configure the Flex Links. Our primary (AT&T) link will use ```GigabitEthernet1/0/50``` and the microwave will use ```GigabitEthernet2/0/48```. Will will also configure a preempt in case the AT&T link is flapping. The ```preemption mode forced``` command will make sure that the AT&T link is the primary link.

```shell
cisco-warehouse#          
interface GigabitEthernet1/0/50
 description P2P-ATT-CID12345
 switchport trunk allowed vlan 105-107,400-401
 switchport mode trunk
 switchport backup interface GigabitEthernet2/0/48
 switchport backup interface GigabitEthernet2/0/48 preemption mode forced
 switchport backup interface GigabitEthernet2/0/48 preemption delay 30
end
!
interface GigabitEthernet2/0/48
 description P2P-Metro
 switchport trunk allowed vlan 105-107,400-401
 switchport mode trunk
end
```
## Checking the Flex Links

Once you have the links configured, make sure to test it. You can check the status of the Flex Links on the Cisco switch via the following command

```shell
show interface switchport backup

Switch Backup Interface Pairs:

Active Interface        Backup Interface        State
------------------------------------------------------------------------
GigabitEthernet1/0/50     GigabitEthernet2/0/48     Active Up/Backup Standby
```

I was surprised how easy this was to setup and well it worked - in all the tests I ran, I only ever saw at most 1 dropped ping.

-eof-