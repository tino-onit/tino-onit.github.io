---
title: "Job Interview Prep and Questions"
date: 2024-12-31 10:00:00 -700
categories: [misc]
tags: [networking,cisco,paloalto,aruba,firewall]    #TAG names should always be lowercase
---
# Job Interview Prep and Questions

Over the years, I've done a number of interviews and I feel I have a good record when it comes to success during the process. I've been offered several jobs that I later turned down for one reason or another.
This has been a private list that I have kept over the years on questions I have been asked and also questions I ask. Some of this info I also pulled from Reddit/Blogs/Forums/discussions or even from talking to friends and co-workers.

## Questions I've been asked

### Core Networking

__What is the native VLAN?__<br>
any traffic that is advertised or received on a trunk port without the 802.1Q VLAN Tag is associated to the native vlan.

__How do you harden a network device?__<br>
Radius, LDAP, SAML, acl on interfaces - source address if outside interface, some sort of rate-limiting DoS protection LLDP on lan-side only

__How to deal with one-way audio issue in voip__<br>
check routing, make sure that clients know how to get to each other

__MTU questions__<br>
- Largest packet that can be sent and received from an interface
- Default is 1500 bytes, anything larger needs to be fragmented
- Can cause network delay, 
- Jumbo frames - larger than default, something like 9000
- Baby giants - 1600 bytes, smaller than 9000

### Spanning-Tree

__BPDU Filter (Bridge Protocol Data Unit)__<br>
filter prevents transmitting out BPDUs

__BPDU Guard__<br>
guard shuts down the port if it receives the BPDU

__How do you make a switch be a root bridge?__<br>
Lowest MAC of all switches is automatically root bridge (default priority = 32768) so to set a root bridge, you can can configure 'spanning-tree vlan 1 priority 8092 (or 4096)

__Spanning-tree portfast__<br>
It bypasses learning and listening phase and useful for DHCP environment

__When would asymmetric routing be a problem?__<br>
If there is a firewall in between, fw doesn't see the session and drops packets

__What does portfast do?__<br>
it bypasses learning and listening phase and useful for DHCP environment

__What network packet unit does Spanning tree use to identify the hierchy of spanning tree topology__<br>
BPDU

__what is the difference between BPDU filter and BPDU guard__<br>

- Filter filters out BPDUs (ignores) from transmitting out.
- Guerde shuts down the port if it receives the BPDU

### Vendor Specific

__Difference between OSPF and EIGRP__<br>
- EIGRP is Cisco
- OSPF is open standard
- EIGRP distance vector - metrics like bandwidth, load, and delays to calculate the shortest
- OSPF link-state - complete map of network, can determine the best path to destination
- EIGRP summarization on "any' router, OSPF on border routers only

### Technologies I know

__What security stuff I've done__<br>
NAC, Aruba ClearPass and Cisco ISE

__Explain what I know about BGP__<br>
tcp port 179
If we have two ISPs and using BGP peering, how can we make one ISP active and the other ISP standby?
Change the local preference for ISP 1 to a higher number then ISP 2 (Default is 100)

__Same scenario of two ISPs, what BGP changes would we need to make so that users visiting our site get routed to a more prefered ISP?__<br>
Use AS-Prepend to pad our AS number on one of the ISP. This will make it appear as if our network is more hops away

exchanging the public prefix routes to ISP and received default route from ISP

__What are the experience with SDWAN__<br>
When I joined, company was moving from Sonicwall to Palo Alto and upgrading the SDWAN edge devices (VeloCloud). We had 2 techs that traveled around the country and did the swap for us. I came up with documentation for them. Different color cables for important cables. Part of the process was for techs to gather information about the local site including ISP information to double-check

__What is administrative distance?__<br>
Administrative distance is the first criterion that a router uses to determine which routing protocol to use if two protocols provide route information for the same destination

__Setup new circuit with ISP?__<br>
Check local config, try setting manual values (duplex and speed, speed 1000 duplex full)
check sfp
call isp

__What is a floating static route__<br>
A conditional static route, set something like if ping 1.1.1.1 = good, stay in route table. if ping drops, remove from route table

### Scenario Based

__User VPN problem__<br>
1. ping internet
2. ping vpn gateway
3. check users dns
4. check monitoring tab for users source ip

__User complaining about not being able to get to some application__<br>

1. Did this just start?
2. Aware of any recent changes?
3. How many people affected?
4. If just them, any recent updates
5. Check network monitor for application state
6. Ping, tracert

__What is native vlan?__<br>
any traffic that is advertised or received on a trunk port without the 802.1Q VLAN Tag is associated to the native vlan.

__If we have two ISPs and using BGP peering, how can we make one ISP active and the other ISP standby?__<br>
Change the local preference for ISP 1 to a higher number then ISP 2 (Default is 100)

__Same scenario of two ISPs, what BGP changes would we need to make so that users visiting our site get routed to a more prefered ISP?__<br>
Use AS-Prepend to pad our AS number on one of the ISP. This will make it appear as if our network is more hops away

__View the route table on a Windows PC__<br>
route print

__Explain what a shadow rule is__<br>
A more broad security rule, that exists above a more specific rule. Specific rule wont match

__Explain Troubleshooting Palo Alto. User trying to access server in a DMZ ( connected via the Palo Alto) but cannot?__<br>
Process should involve basic tools like ping, traceroute. Also checking the monitoring tab for deny statements

__IPSec tunnel between Palo Alto and ASA__<br>
Proxy-IDs

__When building a security rule for a NATed traffic, do you use the pre-nat Zone or the post-nat zone?__<br>
You would use the post-nat zone (but the pre-nat IP) for the security rule 

__In AWS what is the difference between NACL and Security Group__<br>

- NACL is stateless, one directions
- SG is Statefule, smart and knows to allow the return traffic
- NACL is for subnet
- SG is for Instances

__Explain steps to share a TGW across accounts__<br>

1. In main TGW account, create a TGW resource share (Resource Manager).
2. Add account, you want to share with, as principle
3. Goto share account and accept the shared resource
4. Still in share account, goto VPC>Transit Gateway Attachment and Create new Attachment
5. Select the subnets you want to share with the TGW

__In your own words, can you briefly describe what BGP is?__<br>
Exterior gateway protocol that is used to route traffic BETWEEN autonomous systems via distance-vectoring. uses TCP 179
Used when you need to peer with mulitple AS (such as having dual ISPs)

__In OSPF, what is difference between a neighbor and an adjacency?__<br>
Routers are neighbors if they are connected to the same subnet and share common configuration information, but do not share routing information. adjacency is formed between neighbors and they exchange routes. have to be neighbors 1st and then the adjacency can be formed 

__In your own words, can you briefly describe OSPF?__<br>
Interior gateway protocol used to exchange IP routing information. It is a link-state protocol, meaning that the routers exchange information with their nearest neighbor. The topology is known by all routers which allows them to individually calculate the best path to the next hop. Not Cisco proprietary

__What is ARP and how does it work__<br>
ARP work at Layer 2 of the OSI model and is used to translate IP address to MAC address
ARP is broadcast and how switches create their MAC address tables

__What are the two default rules on a Palo Alto firewall and what do they do?__<br>

- intrazone-default = Allow traffic within the same zone
- interzone-default = Deny traffic between zones

__What does the "continue" action do when it is set for a URL category or specific site?__<br>
When users visit that site, they will be presented with a response page saying the page is blocked, but they are able to continue if they want.

__Setup an ASA VPN tunnel__<br>

1. Configuring the Phase1 (IKEv1 or IKEv2)
2. Defining the Tunnel Group and Pre-Shared Key
3. Configuring the Phase2 (IPSec)
4. Configuring the Extended ACL and Crypto Map


## Questions your should ask the employeer
By common definition, the purpose of a job interview is to allow an employer to screen candidates and find someone to fill an open job position. Each and every applicant must prove his or her worth to the potential employer. Many will apply, but only one (or very few) will be selected.

The job interview is as much about you interviewing the company as the company interviewing you. Gauging employee happiness should be high on anyone's "do you have any questions for us" list.

The overall hiring process follows the same sort of pattern. Always aim to impress any potential employer by showcasing your work and showing the best of your abilities. In addition, you should expect any potential employer to reciprocate and make an effort to impress you. When the interviewer finally asks me, "Do you have any questions for me?", I like to conduct a little interview of my own. Here are some of the questions I like to ask potential employers:

- What's the most interesting thing you've worked on since you started at __Potential Co.__?
- How does __Potential Co.__ show that it values the professional development of its employees?
- Are you satisfied with the process and workflow of the team?
- Can you tell me about the company culture?
- Can you tell me about the dynamics of the team I'll be working with
- A year from now, this person has come in and absolutely knocked it out of the park. What does that look like? **(You’ll learn what success looks like)**
- Conversely, 6 months from now, it didn’t work out-you have to move on from this person. What happened? **(You’ll learn what failure looks like)**
- How would you describe the culture? And would you say you’re nurturing that culture or do you want to change it? **(You’ll learn what it feels like when management isn’t in the room)**
- What do the career pathways look like for this role and how often are more senior roles filled via promo vs. outside hire? **(You’ll understand what growth looks like)**
- Will the team be growing in size this year? If so, by how much? **(Growing teams come with challenges but there’s also a ton of opportunity)**
- What does turnover look like? **(If high, this could be a potential warning sign that things may be amiss)**
- What does daily work look like
- Pending projects - sdwan, colo redesign, wireless, ect..
- What will I be doing for 3, 6 months
- Are you happy there?
- What problems is the business-side trying to solve?
- Is there a separate tech and management career path?
- What does work-life balance mean here?


I wouldn't ask questions about pay so soon, unless you're talking to a recruiter. Ask questions about the specific nature of the role, responsibilities and projects, who the manager is, what the team structure is like, why the role has become available, and what are the expectations of the person coming into the role.
