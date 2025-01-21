---
title: "Resizing a Volume on Ubuntu VM"
date: 2023-01-30 10:00:00 -700
categories: [Linux]
tags: [linux, cmd, server]    #TAG names should always be lowercase
---

# Resizing a Volume on Ubuntu VM

At home I use Proxmox as my hypervisor. I have a base template for any Linux based server I want to deploy, but it only has 32GB of disk space. Lately, I've been playing around with Elastisearch and I needed to increase that disk size, due to log collection. The following is an outline of the steps I took to increase the size disk size.

- Resize disk in Proxmox
- Use **fdisk** to learn current layout and if Proxmox resize worked
- Use **parted** to fix the GPT PMBR size mismatch
- Use **parted** to resize the correct partition number
- Use **resize2fs** to resize the actual file system

## First resize the disk in Proxmox

![Proxmox Disk Resize](https://i.imgur.com/ogCZK6i.png)

> The example below, we are resizing from 32 GB to 64 GB. So we want to increase the size by 32 GB.
{: .prompt-info }

![Proxmox Increase Disk Size](https://i.imgur.com/RgQX8pO.png)

1. Select the Hard Disk on the VM you want to resize
2. Select Disk Action
3. Select Resize

Easy peezy! :smile:

## Resize the volume in Ubuntu

Now that we increased the size of the disk, we need to tell our OS - Ubuntu 20.04 - that we want to use this newly available storage space.

> Below screenshots are from when I increased a disk to 90 GB for another project
{: .prompt-info }

We'll use **parted** to resize the volume

```shell
parted /dev/sda
```

Then this command, **print**

Next you’ll get a warning which allows you to “correct” the first error:

```shell
Warning: Not all of the space available to /dev/sda appears to be used, you can fix the 
GPT to use all of the space (an extra 146800640 blocks) or 
continue with the current setting?

Fix/Ignore?
```

To fix it, just type **F** or **Fix**

Next you’ll see this output, indicating our 80 GB drive is to be enlarged and it’s partition # 1:

```shell
Disk Flags:

Number Start End Size File system Name Flags
14 1049kB 5243kB 4194kB bios_grub
15 5243kB 116MB 111MB fat32 boot, esp
1 116MB 85.9GB 85.8GB ext4
```

Now resize the partition

```shell
(parted) resizepart 1

Warning: Partition /dev/sda1 is being used. Are you sure you want to continue?
Yes/No? Y
```

> It will tell you the partition is in use and ask if it’s safe to continue.
{: .prompt-info }

Next tell the system to use all the new space by typing 100%

```shell
End? [85.9GB]? 100%

quit
```

## Resize the filesystem

At this point you still have to resize the file system

```shell
# resize2fs /dev/sda1

resize2fs 1.45.5 (07-Jan-2020)
Filesystem at /dev/sda1 is mounted on /; on-line resizing required
old_desc_blocks = 10, new_desc_blocks = 19
The filesystem on /dev/sda1 is now 39293179 (4k) blocks long.
```

Check if resize worked by typing **df -h**

## Reference

[Resize Proxmox Volume with LVM](https://forum.proxmox.com/threads/resize-disk-of-ubuntu-19-10-vm-on-proxmox.70352/)

-eof-
