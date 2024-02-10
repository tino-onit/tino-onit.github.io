---
title: "ClearPass vs ISE, Terminology"
date: 2023-11-22 10:00:00 -700
categories: [Networking]
tags: [network,cisco,wireless,aruba]    #TAG names should always be lowercase
---

# ClearPass vs ISE, Terminology

In the last few years, two NAC (network access control) solutions have been fighting for the top spot - Aruba ClearPass and Cisco ISE.

Personally, I am a big fan of ClearPass - perhaps because its the first one I've used, setting it up and deploying it the organization. But, when I started at this job they already had Cisco ISE in production so I have been trying to learn it and compare it with ClearPass.

While browsing LinkedIn, I found this nice little article explain some basic differences in terminology.

## ClearPass vs ISE

| ClearPass | ISE | _Notes_|
|----|----|----|
| Policy Manager (CPPM) | Node ||
| Cluster | Cube ||
| Publisher | PAN ||
| Subscriber | PSN ||
| Standby Publisher | Secondary PAN ||
| Services | Policy Sets ||
| Guest | Guest Services ||
| OnBoard | BYOD & Provisioning Services ||
| OnGuard | Endpoint Compliance Services | _Posture Compliance_|
| Profiler | Profiling ||
| Device Insight | Cloud-Based Profiling ||
| Mobility Controller | Wireless Lan Controllers (WLC) ||
| Insight | ISE MnT | _Local to the server_|
| Authentication Method | Authentication Policies ||
| Authentication Sources | External Identity ||
| Enforcement Policies | Authorization Policies ||
| Enforcement Profiles | Authorization Profiles ||



### Sources

[Aruba ClearPass vs Cisco ISE Cheat Sheet](https://yasseraudalab.com/2023/11/20/aruba-clearpass-vs-cisco-ise-cheat-sheet/)