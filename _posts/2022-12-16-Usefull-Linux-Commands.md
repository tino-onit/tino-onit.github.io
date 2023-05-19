---
title: "Useful Linux Commands"
date: 2022-12-16 10:00:00 -700
categories: [Linux]
tags: [linux, cmd, snippet]    #TAG names should always be lowercase
---

# Linux Commands

These are some useful Linux commands that I find myself using over and over. Expect this list to grow over time.

---

## Show top n process for CPU and MEM

```bash
ps -eo comm,pcpu --sort -pcpu | head -5; ps -eo comm,pmem --sort -pmem | head -5
```

### Example

```bash
user01@svr01:~$ ps -eo comm,pcpu --sort -pcpu | head -5; ps -eo comm,pmem --sort -pmem | head -5
COMMAND         %CPU
fwupd            0.8
pihole-FTL       0.1
systemd          0.0
kthreadd         0.0
COMMAND         %MEM
gnome-shell      1.8
Xorg             0.8
fwupd            0.8
systemd-journal  0.6
```

>That's a lot to remember, so instead you can just Alias it. Alias (in shells) is just a nice way to "shortcut" commands.
Using the above as an example, lets Alias it into a easier to remember command

```bash
alias top5="ps -eo comm,pcpu --sort -pcpu | head -5; ps -eo comm,pmem --sort -pmem | head -5"
```

>So now, all we need to do is run "top5" and it will execute our chained command

```bash
milo@svr01:~$ top5
COMMAND         %CPU
fwupd            0.5
pihole-FTL       0.1
systemd          0.0
kthreadd         0.0
COMMAND         %MEM
gnome-shell      1.8
Xorg             0.8
fwupd            0.8
systemd-journal  0.6
```


-eof-