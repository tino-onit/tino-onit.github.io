---
title: "Upgrading Network Gear"
date: 2024-09-04 10:00:00 -700
categories: [misc]
tags: [network,snippet,cmd,troubleshooting,python]    #TAG names should always be lowercase
---


# Upgrading Network Gear

As part of routine maintenance, its a good idea to keep the software/firmware on your network gear updated. Reasons include feature update/releases, bug fixes, and security patches.
What I do on my WinX workstation is
1. Create a file directory. I like to do this directly under the C: drive, ie. ```C:\tftp-root\```
2. Download software/firmware from the hardware vendor site and save to newly created folder
3. Open Powershell, change to directory, and run ```python -m http.server``` (assuming you have Python installed on your system, this will run a webserver in the current directory)

The Powershell window will look something like this

```Powershell
    Directory: C:\tftp-root


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
d-----         8/13/2024   2:49 PM                Backup-ISE
d-----         5/25/2023   8:31 PM                configs
-a----         8/15/2024   5:10 PM       34027520 c2960cx-universalk9-tar.152-7.E10.tar
-a----         8/13/2024  11:14 AM       26786816 c2960x-universalk9-mz.152-7.E10.bin
-a----         8/13/2024   3:19 PM       41564160 c2960x-universalk9-tar.152-7.E10.tar
-a----         8/13/2024  11:14 AM       22966272 c3560cx-universalk9-mz.152-7.E10.bin
-a----          9/3/2024   5:35 PM       25763840 c3750e-universalk9-tar.150-2.SE12.tar
-a----         8/28/2024  12:57 PM       30720000 c3750e-universalk9-tar.152-4.E10.tar
-a----         8/13/2024  11:11 AM      450582304 cat3k_caa-universalk9.16.09.08.SPA.bin
-a----         8/13/2024  11:14 AM      482611915 cat3k_caa-universalk9.16.12.11.SPA.bin
-a----         8/13/2024  11:15 AM     1030752106 cat9k_iosxe.17.06.07.SPA.bin
-a----         8/13/2024  11:15 AM     1258131142 cat9k_iosxe.17.09.05.SPA.bin
-a----         8/16/2024   9:46 PM          26370 tftp.log


PS C:\tftp-root> python -m http.server
Serving HTTP on :: port 8000 (http://[::]:8000/) ...
```

As you can see, the web server is running on port 8000 (by default) and you are serving up your firmware upgrades. Go ahead and use this to pull the firmware images to your gear - it will be much faster than tftp.

## Cisco Gear

### WS-C2960X

```archive download-sw /leave-old-sw /reload http://<SERVER_IP>:8000/c2960x-universalk9-tar.152-7.E10.tar```

What will this do?
```/leave-old-sw``` Leave old sw installed after successful sw upgrade. Useful if you might need to rollback
```/reload``` Reload system (if no unsaved config changes) after successful sw upgrade

### C9300X

```request platform software package install switch all file http://<SERVER_IP>:8080/cat9k_iosxe.17.06.07.SPA.bin new auto-copy verbose```

Or, if you've already copied the file to the local flash and just need to install, you can run the following
```request platform software package install switch all file flash:<version> new auto-copy verbose```

Confirm install is done
```more flash-1:packages.conf | begin rp_boot```

Confirm the boot variable shows the new version
```sh boot```

Reboot and install
```reload```

Clean up old files
request platform software package clean switch all

### C9500

The nice thing about the 9500's is the ability to perform an ISSU upgrade - provided you have Stackwise Virtual configured


```bash
install add file http://10.2.10.20:8000/cat9k_iosxe.17.06.07.SPA.bin
install activate issu
install commit
```

-eof-