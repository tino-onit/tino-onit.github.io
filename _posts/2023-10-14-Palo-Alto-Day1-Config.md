---
title: "Palo Alto NGFW Day 1 Configuration"
date: 2023-10-14 10:00:00 -700
categories: [paloalto, firewall, snippet]
tags: [network, firewall, paloalto, config]    #TAG names should always be lowercase
---
# Palo Alto NGFW Day 1 Config
 I always like to the use Palo Alto's Day 1 configuration file for when setting up new firewalls. The below text is copied from the Palo Alto site

### What does Day 1 Configuration, and what are the advantages of running it?

- The Day 1 Configuration tool helps you configure your devices for threat prevention using best practice recommendations from Palo Alto Networks.

Instead of extensive and detailed "how-to" documentation, Day 1 Configuration templates provide an easy-to-implement configuration model that is use case agnostic. The emphasis is on key security elements, such as: dynamic updates, security profiles, rules, and logging that should be consistent across deployments.


### Why Use Day 1 Configuration Templates?

Day 1 configuration templates play use common best practice recommendations and compiles them into pre-built Day 1 Configuration templates.   These templates can then be loaded into Panorama or a next-generation firewall. Benefits of Day 1 Configuration templates include:

- Faster time to implement
- Reduce configuration errors
- Improve security posture

Once you register a new firewall, you will be presented with the option to download the Day 1 config.
![Sample](https://lh5.googleusercontent.com/iQoPA1-dVJjPbN2yXIHFr0N6mJZZIZy9u2OYkSvNBsn3mERA_X-LAqo2QN6oprzFIOTHmVxwKEf8Dt5TNcEMGn-2jXqZHeOXJssQ8pS1FOYUdHbSyWLVulBM-DRoVshp-opKxOQG)

Additionally, you can read more about the configuration [here, at the IronSkillet website](https://iron-skillet.readthedocs.io/en/docs_master/overview.html).

### If you already have a NGFW deployed but still want to take advantage of the various pieces of configuration, like the various Security Profiles, then below I have the code snippets I use


