---
title: "How to setup multiple Palo Alto VM-Series into same RG in Azure"
date: 2022-10-29 10:00:00 -700
categories: [Palo Alto]
tags: [cloud, azure, vnet, firewall, paloalto]    #TAG names should always be lowercase
---

# How to setup two Palo Alto VM-Series in the same resource group in Azure.

> This is a quick post, but I plan editing it later to add details

When deploying a Palo Alto VM-Series firewall via the Azure Marketplace, you are required to create a new Resource Group (RG) for the deployment. This means that you'll need a separate RG for each firewall you deploy.

One way to get around this is to deploy your first firewall and get it up and running. Then, when the deployment is complete, there will be a "Download Deployment Template" button. You click on that and download the template, modify it accordingly - this mostly consists of changes names to fit your scheme, i.e. ~~FW01~~ > FW02.  
Redeploy the template and it will use the same RG that was used for the first firewall.

:cloud: :bowtie:


-eof-