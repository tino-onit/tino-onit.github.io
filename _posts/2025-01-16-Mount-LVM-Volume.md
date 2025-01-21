---
title: "How to mount an LVM volume in Linux"
date: 2025-01-16 10:00:00 -700
categories: [Linux]
tags: [linux, cmd, snippet]    #TAG names should always be lowercase
---

I recently needed to rebuild my [Eve-NG](https://www.eve-ng.net/) server - I built a lab, with a Palo Alto firewall. I forgot to turn it off and the traffic log data filled up my disk. I couldn't log into Eve-NG to turn the lab off or anything.

Luckily, the data on the disk was intact but I needed a way to extract it.

I came across
[Jason Doolittle Website](https://www.megajason.com/2021/07/12/how-to-mount-an-lvm-volume-in-linux/) while searching for how to mount the LVM volume onto my Debian vm. I needed to mount it so that I can extract the ISO's, configs, and more importantly - the labs that I built.

Here is a summary of the steps I took, a simplified version of what Jason has posted.

Install LVM2 tools, if you don't have them

```bash
sudo apt install lvm2
```

Run ```pvscan``` to scan for physical volumes

```bash
sudo pvscan --cache
```

If you check the output, the physical disk you want to retrieve files from should be listed

Get the name of the volume group

```bash
sudo vgscan
```

Your output might look something like this

```bash
Found volume group "ubuntu-vg" using metadata type lvm2
```

Now you can activate the volume group

```bash
sudo vgchange -ay your-vg-name
```

Output should be something like

```bash
1 logical volume(s) in volume group "ubuntu-vg" now active
```

Find the name of the logical volume

```bash
sudo lvs
```

Example

```bash
LV         VG        Attr       LSize   Pool Origin <truncated to prevent wrapping>
ubuntu-lv  ubuntu-vg -wi-a----- 200.00g 
```

Create a mount point for the volume (optional if you already have a place to mount it). I’ll just put it in under /mnt (the usual place)

```bash
sudo mkdir /mnt/ubuntu-volume
```

Now you can mount it, 

```bash
sudo mount /dev/volume-group/logical-volume /mnt/your-mount-point
```

That should get volume mounted and you should be able to browse its contents. I used this to get all my lab files 
from my (old) Eve-NG SSD.

-eof-