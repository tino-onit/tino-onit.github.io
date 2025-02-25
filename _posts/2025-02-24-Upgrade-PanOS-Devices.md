---
title: "How to Upgrade PanOS Devices"
date: 2025-02-24 10:00:00 -700
categories: [Networking, Palo Alto, Troubleshooting]
tags: [network, cmd, config, troubleshooting, firewall]    #TAG names should always be lowercase
---

# How to Upgrade PanOS Devices

Process for upgrading Palo Alto Panorama and NGFW devices. For the NGFW devices, this is written for HA pairs, but its the same process for single/stand-alone devices.

## Panorama

I'm adding an additional step - upgrading from one major release to another. In this example, from an 8.x.x release to 9.0.8. If you're just going up to a new minor release or patch, its even easier.

1. Save/export tech support and device state and save named device config snapshots
2. Download and install 9.0.0
3. Reboot
4. Save/export tech support and device state and save named device config snapshots
5. Download and install 9.0.8
6. Reboot

Upgrade on Panorama is done. That was pretty easy huh?

## NGFW Firewalls

> **Note:** If using Panorama to manage NGFW firewalls, you need to be running an equal or higher PanOS version, on Panorama.
{: .prompt-warning }

The process for firewall device (physical or virtual) is roughly the same. In this example, I'm doing the upgrade from the devices themselves vs via Panorama. Also, this also walks through upgrading from one major release to another, if you're just going up to a new minor release or patch, its even easier.

It is recommended to upgrade the Primary firewall first and then upgrade the Secondary firewall. To ensure that HA is working properly, you should perform a fail-over. This is done for two reasons:

> Ensure that HA failover is functioning properly

> Ensure that the passive firewall is functioning properly and is able to pass traffic without issues

### Primary firewall

1. Suspend Primary firewall to make Secondary firewall active
    **Verify all applications are working as expected**

2. Save/export tech support and Device state and save named device config snapshots

3. Upgrade Primary firewall ( Device > Software ) _refresh might be needed_
    - Download 9.0.0
    - Install 9.0.0
    - Reboot

4. Verify commits in CLI with `show jobs all` then
    - Download 9.0.8
    - Install 9.0.8

5. Run the following command to make Primary firewall functional again: `request high-availability state functional`

6. This concludes upgrade on the Primary firewall.

### Secondary firewall

1. Suspend Secondary firewall to make Primary firewall active
    **This will cause an HA failover. Production traffic is now going through Primary firewall with new software installed.**
    **Verify all applications are working as expected**

2. Upgrade Secondary firewall ( Device > Software ) _refresh might be needed_
    - Download 9.0.0
    - Install 9.0.0
    - Reboot

3. Verify commits in CLI with `show jobs all` then
    - Download 9.0.8
    - Install 9.0.8
    - Reboot

4. Verify commits in CLI with `show jobs all` again

5. Run the following command to make Secondary firewall functional again: `request high-availability state functional`

6. This concludes upgrade on the Secondary firewall.

## Post-upgrade verification

- Now that both Primary and Secondary firewalls are both running the new software, it is a good idea to test failover functionality again.

- Run the following comma to suspend Primary firewall to fail traffic to the Secondary firewall

`request high-availability state suspend`

- Ask app owners to verify all applications are working on the network through the Secondary firewall. If there is a problem, skip to troubleshooting section.

- Run the following CLI command to make Primary firewall functional again:

`request high-availability state functional`

- Repeat the process to verify traffic works fine through Primary firewall (suspend the Secondary firewall, test functionality on Primary firewall, then re-enable Secondary firewall).

This concludes failover test.

### Some additional things to take note of

- Check system logs for any unexpected errors.
- Check traffic logs for any traffic that is unexpectedly allowed or denied.
- Verify connectivity and network functionality between firewall and panorama, specifically make sure log forwarding from firewall to Panorama is working.
- Also, validate changes will commit between Panorama and the managed devices.
- Check system reports and incidents for any relevant outages.


-eof-