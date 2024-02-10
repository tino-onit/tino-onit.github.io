---
title: "Apple Wireless Roaming"
date: 2023-11-17 10:00:00 -700
categories: [Networking]
tags: [network,apple,wireless]    #TAG names should always be lowercase
---

# macOS Wireless Roaming for Enterprise Customers

This is from a Reddit post that I recently found. I wanted to make sure I kept it for future reference, so I'm posting it here.

[Original Post](https://www.reddit.com/r/networking/comments/17x5ari/apple_has_support_documents_that_explicitly/)

### Trigger threshold

The trigger threshold is the minimum signal level a client requires to maintain the current connection.

macOS clients monitor and maintain the current BSSID’s connection until the RSSI crosses the -75 dBm threshold. After RSSI crosses that threshold, macOS scans for roam candidate BSSIDs for the current ESSID.

Consider this threshold in view of the signal overlap between your wireless cells. macOS maintains a connection until the -75 dBm threshold, but 5 GHz cells are designed with a -67 dBm overlap. Those clients will remain connected to the current BSSID longer than you might expect.

Also consider how the cell overlap is measured. The antennas on computers vary from model to model, and they see different cell boundaries than may be expected. It's always best to use the target device when you measure cell overlap.

### Selection criteria for band, network, and roam candidates

macOS always defaults to the 5 GHz band over the 2.4 GHz band. This happens as long as the RSSI for a 5 GHz network is at least -68 dBm and the load on the network is not excessive.

macOS considers information shared by networks about channel utilization and quantity of associated clients. macOS uses these details along with signal strength measurements (RSSI) to score candidate networks. Higher score networks offer a better Wi-Fi experience.

If multiple 5 GHz SSIDs receive the same score, macOS chooses a network based on these criteria:

```shell
    802.11ax is preferred over 802.11ac.

    802.11ac is preferred over 802.11n or 802.11a.

    802.11n is preferred over 802.11a.

    80 MHz channel width is preferred over 40 MHz or 20 MHz.

    40 MHz channel width is preferred over 20 MHz.
```

macOS Monterey supports 802.11k on Mac computers with Apple silicon.

Earlier versions of macOS don't support 802.11k but do interoperate with SSIDs that have 802.11k enabled.

macOS selects a target BSSID whose reported RSSI is 12 dB or greater than the current BSSID’s RSSI. This is true even if the macOS client is idle or transmitting/receiving data.
Roam performance

Roam performance describes how long a client needs to authenticate successfully to a new BSSID.

Finding a valid network and AP is only part of the process. The client must complete the roam process quickly and without interruption so the user doesn't experience downtime. Roaming involves the client authenticating against the new BSSID and deauthenticating from the current BSSID. The security and authentication method determines how quickly this can happen.

First, 802.1X-based authentication requires the client to complete the entire EAP key exchange. Then, it can deauthenticate from the current BSSID. Depending on the environment’s authentication infrastructure, this might take several seconds. End users could experience interrupted service in the form of dead air.

macOS supports static PMKID (Pairwise Master Key identifier) caching to help optimize roaming between BSSIDs in the same ESSID. macOS doesn't support Fast BSS Transition, also known as 802.11r. You don't have to deploy additional SSIDs to support macOS because macOS interoperates with 802.11r.

macOS Monterey supports 802.11r and 802.11v on Mac computers with Apple silicon.

macOS supports static PMKID (Pairwise Master Key identifier) caching to help optimize roaming between BSSIDs in the same ESSID. Earlier versions of macOS don't support Fast BSS Transition, also known as 802.11r. Earlier versions of macOS interoperate with 802.11r so that additional SSIDs don't need to be deployed.

### Sources

[Original Post](https://www.reddit.com/r/networking/comments/17x5ari/apple_has_support_documents_that_explicitly/)

[macOS wireless roaming for enterprise customers](https://support.apple.com/en-us/102002)


### Additional Reading

[About wireless roaming for enterprise](https://support.apple.com/en-us/HT203068)

[Wi-Fi network roaming with 802.11k, 802.11r, and 802.11v on iOS, iPadOS, and macOS](https://support.apple.com/en-us/103274)

-eof-