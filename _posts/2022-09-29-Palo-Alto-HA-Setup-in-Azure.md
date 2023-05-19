---
title: "How to setup Palo Alto VM-Series in HA in Azure"
date: 2022-09-29 10:00:00 -700
categories: [Palo Alto]
tags: [cloud, azure, vnet, firewall, paloalto]    #TAG names should always be lowercase
---

# Palo Alto VM-Series Firewalls on Azure

I recently completed a project where I deployed a pair of VM-Series firewalls, in an Active/Passive HA setup, on Azure using a "transit" VNet. A VNet on Azure is just a defined address space where you create subnets for NIC's to live in.  

One of the caveats is that you have to be ok with 2min-5min of downtime (ie. no traverse traffic) while the Passive firewall transitions to Active. This is because - as you'll see below - this design uses a "floating IP" configured in Azure.  

This "floating IP" becomes the default gateway for egress and ingress traffic.  

```
.
├── VNet (10.0.0.0/16)
 └── Subnet (10.0.1.0/24, 10.0.2.0/24, ect..)  
  └── NIC (10.0.1.10/24) 
   └── Security Group  
```

This link explains Microsoft best practices for deploying resources across Subscriptions and VNETs  
[ ​https://docs.microsoft.com/en-us/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-networking ](​https://docs.microsoft.com/en-us/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-networking)

This link is the Palo Alto documentation, on deploying VM-Series in Azure  
[ https://docs.paloaltonetworks.com/vm-series/9-1/vm-series-deployment/set-up-the-vm-series-firewall-on-azure/configure-activepassive-ha-for-vm-series-firewall-on-azure](https://docs.paloaltonetworks.com/vm-series/9-1/vm-series-deployment/set-up-the-vm-series-firewall-on-azure/configure-activepassive-ha-for-vm-series-firewall-on-azure)

## Assumptions and Prerequisites:

---

- A valid active subscription.
- A dedicated resource group.
- This design uses IPv4 IP addressing. IPv6 is available but is not covered.
- This deployment was tested predominantly in the West Central US region, although deploying this design should be possible in any Azure region.
- Licenses for primary and secondary - if used - Palo Alto firewalls.
  - Although not really needed, since you can see traffic in the monitor tab without a license in > 10.1.x PanOS.

## VNet Setup

---
I have Created two VNETs - You can use one subscription or multiple

- VNet-HUB - Also known as Transit 
- VNet-SPOKE - Where your workloads will sit. Can also be a peered vnet, 3rd party or own.

I deployed via ARM template - which is the easiest way to launch two vm-series firewalls into the same RG (Resource Group). Otherwise, if launching one firewall at a time via the Marketplace, you can only put them in separate RG's (but I have a "trick" to this).

[PANW ARM Template](https://github.com/PaloAltoNetworks/Azure-HA-Deployment)

## Azure VM-Series HA Config

---
Once you have both VM-Series firewalls created (and in the same RG) we can start to configure the Active and Passive Node from Azure side

> Take a Look on the below design which is shared from the Palo Alto Portal, we will follow almost the same*

![PANW Network Diagram](https://i.imgur.com/YJbrN98.png)

As we can see from the below NICs Configuration on my Palo Alto Nodes, we have:

- Trust Interface
- Untrust Interface
- MGMT Interface
- HA-Interface

In order to configure the HA interface, we need to create it. (This is assuming you have an HA subnet)

1. Power off your VM's 
2. Add a new NIC to each VM, in Azure, and assign it an IP in the HA subnet
3. Power on the VM's and configure the new NIC == Ethernet1/3 == as DHCP
4. Follow the Palo Alto guide on setting up HA

## Azure Application Registration

---
Next task is creating an APP and registering it with the right permissions in order to make the resources "IP" floating between both Firewall Nodes. This is an Azure APP and we will use some of the output config to setup the VM-Series plug-in, inside out VM firewalls.

1. Login to Azure Portal
2. Click on Azure AD
3. From App registration > Click on +New registration 
4. Enter the App name and you can leave the rest of the options as a default, once App is created make sure to write down these configuration (highlighted in Yellow) as you will use them in Palo Alto Configuration
5. Next step is to create a Key secret, go to Certificates & Secret  > Client Secret > New Client Secret 
6. Enter the Client Description and I Recommended to set the Expires Value "Never"
7. Next Step is to Add API Permissions, from API Permissions > + Add a Permission > Select Microsoft Graph
8. Add the permissions as above figure
9. After that, we need to add permissions on the subscription level for the deployed APP
10. Go to Home > Subscriptions
11. Access Control (IAM) > +Add > Add Role Assignment
12. Select Contributor Role  and from Select > select the App name
13. Lastly, you'll need to go to your firewall and copy the settings to the VM-Series plug-in
![PANW vm-series plug-in](https://i.imgur.com/3IVkVOF.png)

## Azure VM-Series Interface Config

---
Now we will Add the new Floating IP on the Node 1

1. Go to the Palo Alto VM Node 1 > Select Networking
2. You will see the 4 Network interfaces which we have added before
3. Click on the Untrust "Second NIC" > a new windows will open
4. From IP Configurations > Click on Add
5. Enter the Private floating IP Name e.g 192.168.10.100
6. Click on Create
7. Repeat the Steps for the Trust NIC as Well  

> Note: With floating IP address, it can quickly move the IP address from the active firewall to the passive firewall during failover. You will also need HA links – a control link and data link to synchronize data and maintain state information between the peers for the passive firewall to seamlessly secure traffic as soon as it becomes the active peer.

Here you can see what the floating IP (and additional public IPs) look like from the Azure side.
![Azure PANW firewall interface](https://i.imgur.com/L8FgbtY.png)

## VM-Series Configuration

---
Next Step is to Login to Palo Alto Firewall and start the initial configuration and it will be the last Part

1. Login to 1st Palo Alto Firewall using the public IP or DNS name
2. Enter the credentials
3. On the Main screen you will see the current active firewall in case you have active/passive scenario  
4. Next Step is assigning the IPs for the Trust interface, including the Floating IPs  
![PANW Trust Interface IPs](https://i.imgur.com/gxA1sBb.png)
5. Next, select the Untrust interface and assign the IPs, make sure to include the floating and any additional IPs (if you're hosting services)  
![PANW UnTrust Interface IPs](https://i.imgur.com/692ajw3.png)
6. Then you have to Add a Virtual Route between the Interfaces (trust and untrust)
    - DefaultRoute (0.0.0.0/0) interface Ethernet1/1 next-hop 10.250.16.1 (the gateway IP of your "untrust" subnet)
    - To-Transit-VNet (10.250.16.0/20) interface Ethernet1/2 next-hop 10.250.17.1 (the gateway IP of your "trust" subnet)
7. You can add multiple  Static Route between your subnets  
Below is an example:  
![PANW Virtual Router](https://i.imgur.com/zgREi1t.png)
8. At this point, you are ready to add new Policies and Objects to your firewalls, using the Device Groups tabs in Panorama, and configure more Network and Device settings using the Templates tabs. 

## Routing Issue, Inside Azure

---
One thing to note, in my case, the firewall was not routing traffic to my branch sites (via 10.90.1.10, as seen in the route table above). This is was a result of the firewall not being able to arp for the MAC address of that next-hop  
![PANW ARP table, before](https://i.imgur.com/CwDH8ty.png)

After a bit of googling, I came across a Reddit post of someone experiencing something similar. They opened a case with PANW and ended up doing the following  
Under the Trust interface (or possibly even the UnTrust, if that is the interface of the next-hope) just add a manual ARP entry  
![PANW Trust Interface, ARP Entries](https://i.imgur.com/nHcBtrk.png)  
Then it will be displayed in the firewalls ARP table and the firewall will forward to that next-hop  
![PANW ARP table, after](https://i.imgur.com/zHmcDWC.png)

> Note: The MAC address is ``` 12:34:56:78:9a:bc ``` which is the same as the other interface. It's a cloud (Azure) thing

## Azure Route Tables

---
To finish this off, here you will see what the various Azure route tables look like  
Transit Firewall Trust route table
![Azure Trust RT](https://i.imgur.com/rwsCtO7.png)  

Transit Firewall UnTrust route table
![Azure UnTrust RT](https://i.imgur.com/3ekmlkK.png)  

Spoke route table (applied to all spoke VNet subnets). 
Make note of the only UDR, 0.0.0.0/0 ==> FW trust floating IP
![Azure Spoke RT](https://i.imgur.com/vZhmAIs.png)  

## SDWAN Route Tables

---
If using SDWAN, other if you need to forward the traffic AFTER the firewall
SDWAN Trust route table
![Azure SDWAN Trust RT](https://i.imgur.com/QIwqcoL.png)
SDWAN UnTrust route table
![Azure SDWAN UnTrust RT](https://i.imgur.com/Hb4YZuJ.png)


-eof-