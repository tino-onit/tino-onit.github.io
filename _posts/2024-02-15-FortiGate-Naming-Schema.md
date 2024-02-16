---
title: "FortiGate Naming Schema"
date: 2024-02-15 10:00:00 -700
categories: [misc]
tags: [network,fortinet,firewall,paloalto,cisco]    #TAG names should always be lowercase
---


# FortiGate Naming Schema

A suggested nomenclature for FortiGate firewall configurations, as first documented by the illustrious [/u/VeryStrongBoi](https://www.reddit.com/user/VeryStrongBoi/) in the following thread:

[Firewall object naming - What's your standard?](https://www.reddit.com/r/fortinet/comments/q1vqfi/firewall_object_naming_whats_you_standard/)

> **Note:** The following information is for _inspiration_ only. No responsibility is assumed for any configuration implemented based on these suggestions.
{: .prompt-info }

## Guiding Principles

### **Character Limitations**

FortiOS has some limitations regarding lengths of object names, so try to use shorter names or abbreviations, where possible, with the comments fields being used for additional elaborations where needed.

### Case Sensitivity

ForitOS runs on Linux, and Linux is case sensitive, so do not mix upper and lower case. The preference should be for all object names to be lower case, except for objects that are by default all upper case, such as service objects, so new/custom service objects should also be all upper case.

### White Space

White space can create difficulties for parsers for many automation, documentation, and logging systems, so spaces should never be used in object names, and instead dashes `-` should be used to separate words.

### Special Characters

Special characters can also create difficulties for parsers for many systems so the only special characters that should be used in object names are dashes `-` and underscores `_`, and never characters like `! @ # $ % ^ & * ( ) ~ + =`, etc., even when FortiOS itself allows it.

### Mutability

Unchangeable names (e.g., [interfaces](https://www.notion.so/FortiGate-Naming-Schema-c4e37d59bdea49e09d62e62bb6618343?pvs=21)) should be named in such a way that they never need to be changed, so they should be named according to their objective _essence_ (which cannot change) rather than their _use case_ (which can change). The **alias** field (which is changeable) can then be named according to the current use case.

For names which can easily be changed (e.g., [address objects](https://www.notion.so/FortiGate-Naming-Schema-c4e37d59bdea49e09d62e62bb6618343?pvs=21)) they should be named according to their use case or their FQDN.

### Object Types

There can be many different types of objects that share the same use case, and so would share the same name, based on the above principle, but sharing the same name may be impossible in FortiOS in some cases, and even when possible, will still be confusing for humans. Therefore, each name should be **suffixed** by its object type, separated by an underscore `_`.

**Example:** Imagine a webserver whose FQDN is `web01.example.local`, and who has a private IP address of `192.168.1.11`, and a public IP address of `4.4.4.4`, with a 1-to-1 VIP object performing NAT.

In this case, the private IP address object would be named `web01.example.local_priv` (for "private IP"), whereas the VIP object would be named `web01.example.local_vip`.

> Additionally, you might need a MAC address object for the same server, which would be named `web01.example.local_mac`.
{: .prompt-info }

### Numbering

Multiple instances of the same object type for the same use case should be suffixed with a number that increments from 1. If you expect to never need more than 9 of an object, this can be a 1-digit number, but if you may need up to 99 of an object, it should be a 2-digit number, etc.

> **Example:** If `web01.example.local` needed two different private IP addresses objects (such as if it were dual-homed), these would be named `web01.example.local_priv01` and `web01.example.local_priv02`.
{: .prompt-info }

### Object Type Groups

Group names should also be suffixed with their type, but will be of the **plural** form, indicating a collection of more than one of that object.

> **Example:** A group of `_priv` objects would be named `_privs`. A group of `_vip` objects would be named `_vips`. A group of `_mac` objects would be named `_macs`, etc.
{: .prompt-info }

## Interfaces

### Loopback Interface

- **Name:** `loN`, where _lo_ means "loopback" (like in Cisco terminology) and _N_ is the number of the loopback interface, which increments from 1.
- **Alias:** `<use-case>_loopback`, where _<use-case>_ is what this loopback will be used for.

> **Example:** If you just had a single loopback for all management function, then the alias for `lo1` would be `mgmt_loopback`. If you were going to have different loopbacks for different management functions, you might have something like `lo1:ntp_loopback`, `lo2:dns_loopback`, `lo3:bgp_loopback`, etc.
{: .prompt-info }

### LACP 802.3ad Aggregate Interface

- **Name:** `poN`, where _po_ means "port channel" (like in Cisco terminology) and _N_ is the number of the port channel, which increments from 1.
- **Alias:** `<use-case>_trunk`, where _<use-case>_ is what this trunk will be used for, and __trunk_ is Fortinet's terminology for a LACP Aggregate.

> **Example:** `po1` might have an alias of `core_trunk`.
{: .prompt-info }

### Hardware Switch Interface

- **Name:** `swN`, where _sw_ means "switch" and _N_ is the number of the port channel, which increments from 1.
- **Alias:** `<use-case>_switch`, where _<use-case>_ is what this switch will be used for*.*

> **Example:** `sw1` might have an alias of `lan_switch`.
{: .prompt-info }

> **Tip:** Consider disabling all unused physical ports on the FortiGate (for both security and stability reasons). Put all of them into a special switch object named `sw0` with alias `disabled_switch`, and then just disable that whole switch object.
{: .prompt-tip }

### Software Switch

**Never use these, because they can't do hardware acceleration.** The only time to use a software switch is if you have a FortiGate that can't support hardware switches, in which case, you should try to just use a LACP Aggregate to a physical switch to do your switching.

If you must use a software switch, just follow the same naming convention for a hardware switch.
{: .prompt-warning }

### Physical Interface

Physical interfaces cannot be renamed, but also **they should never be used in your configuration**, because they are completely inflexible and will create reference hell for you if you ever need to add/change the underlying physical interfaces. So instead, always create either a LACP Aggregate or a Hardware Switch Interface to add your physical interfaces to, even if you only need a single interface today.
{: .prompt-warning}

### NPU VDOM Link

- **Name:** These are hard-coded as `npuN-vlinkM`, where _N_ is the number of NPUs, incrementing from 0, and _M_ is the vlink number (either 0 or 1).
- **Alias:** These cannot have aliases.

> **Tip:** We will not be using the NPU VDOM Links themselves, but instead will be using 802.1q VLAN sub-interfaces on the VDOM links, so that we can have up to 4096 hardware-accelerated VDOM links per NPUs.
{: .prompt-tip }

### Software VDOM Links

Do not use these, because they cannot be hardware accelerated.

### VLAN Interfaces

- **Name:** `<base-interface>.VVVV`, where _<base-interface>_ is the base interface of which this VLAN interface is sub-interface, and _VVVV_ is the 4-digit VLAN ID according to 802.1q.
- **Alias:** `<use-case>_vlan`

> **Examples:** 
> - `po1.0002` might have an alias for `inf_vlan` (where _inf_ means > infrastructure, such as servers and management IPs of network infrastructure).
> - `po1.0003` might have an alias of `user_vlan`.
> - `po1.0004` might have an alias for `voip_vlan`.
> - `po1.0005` might have an alias of `guest_vlan`.
> - `po1.0006` might have an alias of `iot_vlan` (where _iot_ means Internet of > Things, such as printers, cameras, building automation sensors, and other low-security headless devices).
> - Alternatively, if you're using a hardware switch instead of LACP Aggregate, > `sw1.0002` might have an alias for `inf_vlan`, etc, as above.
> - `npu0-vlink0.0002` might have an alias of `<vdom01>_vlink` while `npu0-vlink1.0002` might have an alias of `<vdom02>_vlink` (thus linking _vdom01_ with _vdom02_)
{: .prompt-info }

### SSID Interfaces

- **Name:** `ssidN`, where _N_ is the number of SSID interface, which increments from 1.
- **Alias:** `<use-case>_ssid`, where _<use-case>_ is what this SSID will be used for.

> **Example:** `ssid1` might have an alias of `corp_ssid`, and `ssid2` might have an alias of `guest_ssid`.
{: .prompt-info }

### IPsec VPN Phase-1 Interfaces

- **Name:** `<use-case>_tunN`, where _tun_ means "tunnel" and _N_ is the number of the tunnel that increments from 1.
- **Alias:** As of FortiOS 6.4, IPsec VPN Phase-1 Interfaces cannot have aliases, which is why the use-case is in the name instead, so the use-case name must be relatively short/abbreviated.

### Zones

- **Name:** `<use-case>_zone`
- **Alias:** Zones cannot have aliases as of FortiOS 6.4, but you can use the comments field for additional elaboration if needed.

> **Examples:**
>
> - `inside_zone` might be the name of for the zone that will collect all of the "inside" interfaces protected behind this FortiGate.
> - `wan_zone` might be the name for the (SD-WAN) zone that will collect all of the "wan" interfaces connected to this FortiGate.
> - `tun_zone` might be the name for the (SD-WAN) zone that will collect all of the "tunnel" IPsec interfaces connected to this FortiGate.
{: .prompt-info }

> **Tip:** All interfaces that will have IPv4/6 firewall policies applied should be added to a zone, such that all policies will be _zone_-based and not _interface_-based, which will greatly enhance policy consolidation, and make future interface changes/additions much easier and quicker.
{: .prompt-tip }

## Address Objects

### Device/MAC Objects

- **Name:** `<fqdn>_mac`. Comments can be used for additional elaboration.

### Subnet Objects

For a /32 in the RFC 1918 (private) IP space:

- **Name:** `<fqdn>_priv`, or `<fqnd>_privNN` if more than one __priv_ will be needed for that same FQDN.

For a /32 outside of the RFC 1918 space:

- **Name:** `<fqdn>_pub` or `<fqnd>_NN` if more than one __priv_ will be needed for that same FQDN

For prefix shorter than /32:

- **Name:** `<use-case>_subnet`, or `<use-case>_subnetNN` if more than one __subnet_ will be needed for that same use-case.

Comments can be used for additional elaboration.

### FQDN Objects

- **Name:** `<fqdn>_fqdn`. Comments can be used for additional elaboration.

### GeoIP Objects

- **Name:** `<country-abbreviation>_geo`

### IP Range Objects

- **Name:** `<use-case>_range`. Comments can be used for additional elaboration.

> **Tip:** Do not use a __range_ object when a _subnet_ object could be used instead.
{: .prompt-tip }

### Custom ISDB Objects

- **Name:** `<use-case>_isdb`

### VIP Objects of Type _static-nat_

For a 1-to-1 Destination NAT:

- **Name:** `<fqdn>_vip`

For a _N_-to-_N_ Destination NAT range:

- **Name:** `<use-case>_vip`

For a 1-to-M Destination PAT:

- **Name:** `<fqdn>-<service>_vip`

> **Example:** `web01.example.com-ssh_vip` for port 22 and `web01.example.com-http_vip` for port 80.
{: .prompt-info }

### Address Group Objects

- **Name:** `<fqdn | use-case>_<object-type>s`, where the _s_ indicates plurality.

> **Examples:**
> 
> - `company-asn_subnets` might contain all of the subnets for the company's ASN such as `company-asn_subnet01` and `company-asn_subnet02`.
> - `ban_geos` might include all of the Geo IP objects that you want to block, such `china_geo` and `iran_geo`.
> - `sslvpn_geos` might include all of the Geo IP object for which you want to allow SSL VPN connections.
> - `web01.example.com_vips` might contain both `web01.example.com-ssh_vip` and `web01.example.com-http_vip`.
> - `hairpin_vips` might contain all of the VIP objects for which you want to allow hairpin NAT.
> - `collab_isdbs` might contain the ISDB objects for Zoom, Teams, WebEx, Slack, etc.
{: .prompt-info }

> **Tip:** Groups should always be preferred over individual objects for creating policies and routes, as this makes it easier to facilitate changes/additions in the future.
{: .prompt-tip }

## Special Address Objects

### VIP Objects of type _server-load-balance_ (aka _Virtual Servers_)

- **Name:** `<cluster-name>:<port>_vlb`, where _vlb_ means "virtual load balancer".

### IP Pools (aka _Source NAT objects_)

For an overload pool:

- **Name:** `<use-case>_overload`

For a 1-to-1 pool:

- **Name:** `<use-case>_one-to-one`

As of FortiOS 6.4, neither VIP Objects nor IP Pools can be added to groups.

## Service Objects

### Single Service Object

- **Name:** `<SERVICE-NAME>` in **ALL CAPS**, because all of the default service objects are in all caps.

> **Special Case:** Alternate ports for default service(s) should be suffixed with _-ALT01._
>
> - E.g., `HTTPS_ALT01` could be TCP/9443, and `HTTPS-ALT02` could be TCP/10443, etc.
{: .prompt-info }

### Service Group Objects

- **Name:** `<use-case>_services`, lower case.
> **Examples:**
>
> - `web_services` might contain HTTP, HTTPS, and ALL_ICMP.
> - `webamin_services` might contain SSH, HTTP, HTTPS, and ALL_ICMP (where _webamin_ is short for "web administration").
{: .prompt-info }

> **Tip:** Service groups should be preferred in policies to facilitate easier changes/additions.
{: .prompt-tip }

> **Tip:** _ALL_ICMP_ should generally be added to most service groups to allow for easier troubleshooting and better functionality of services like PMTUD, although security considerations in some cases might warrant restrictions on some ICMP types and codes.
{: .prompt-tip }

## Security Profiles

### Antivirus

- **Name:** `<use-case>_av`

> **Example:** You might have both `flow_av` and `proxy_av`.
{: .prompt-info }

### Web Filter

- **Name:** `<use-case>_wf`

> **Example:** You might have both `corp_wf` and `guest_wf`.
{: .prompt-info }

### DNS Filter

- **Name:** `<use-case>_df`

> **Example:** You might have both `corp_df` and `guest_df`.
{: .prompt-info }

### Application Control

- **Name:** `<use-case>_ac`

> **Example:** You might have both `corp_ac` and `guest_ac`.
{: .prompt-info }

### Intrusion Prevention

- **Name:** `<filters>_ips`

> **Example:** You might have both `client_ips` (to protect clients that connect outbound to internet) and `win-server-iis_ips` (to protect a Windows IIS server), as well as `lin-server-appache_ips` (to protect a Linux Apache server), etc.
{: .prompt-info }

> **Tip:** Using as specific of an IPS filter as possible for a given flow will greatly improve the efficiency of the IPS Engine's processing.
{: .prompt-tip }

### WAF

- **Name:** `<use-case | fqdn>_waf`

> **Example:** You might have `web01.example.com_waf` and `web02.example.com_waf`, with different configurations for two different web servers that you need to protect.
{: .prompt-info }

### SSL Inspection

- **Name:** `<use-case | fqdn>_ssl`

> **Examples:**
>
> - For basic outbound certificate inspection, you might have `client-cert_ssl`.
> - For deep SSL inspection outbound, you might have `client-deep_ssl`.
> - For deep SSL inspection inbound protecting a single server (or multiple servers with SNI in FortiOS 7.0 or higher), you might have `web01.example.com_ssl`.
{: .prompt-info }

### Protocol Options

- **Name:** `<use-case>_prot`

> **Example:** You might have both `default_prot` and `comfort-clients_prot`.
{: .prompt-info }

## Firewall Policies

For an _**allow**_ policy:

- **Name:** `[this]>[that]`, where _[this]_ is the name of the source and _[that]_ is the name of the destination.

> **Examples:**
>
> - `lan_subnets>wan`
> - `corp_subnets>dc01.example.com`
{: .prompt-info }

For a _**deny**_ policy:

- **Name:** `[this]>[that]_deny`, where _[this]_ is the name of the source and _[that]_ is the name of the destination.

> **Example:** `lan_subnets>ban-geos_deny`
{: .prompt-info }

## User and Authentication Objects

### User Group

- **Name:** `<group-name>_users`

### LDAP Server

- **Name:** `<fqdn-of-server>_ldap`

### RADIUS Server

- **Name:** `<fqdn-of-server>_radius`
