---
title: "Initial config of a Linux server"
date: 2022-09-21 10:00:00 -700
categories: [Linux]
tags: [linux, cmd, server, snippet]    #TAG names should always be lowercase
---

# Initial config of a Linux server

 This is something - which I've gathered from different resources - that I do whenever I setup a new Linux server or workstation. In general, this would work with any distribution but I mostly use Ubuntu and Debian.

## Create a new user (if not done during install)

We need to create a new user so as to not use the root account for general administration - and hopefully we made sure to set a really good root account password ;)

Create the new user

```bash
adduser <username>
```
Any you'll be presented with some prompts

```bash
root@server1:~# adduser user1
Adding user `user1' ...
Adding new group `user1' (1000) ...
Adding new user `user1' (1000) with group `user1' ...
Creating home directory `/home/user1' ...
Copying files from `/etc/skel' ...
New password: 
Retype new password: 
passwd: password updated successfully
Changing the user information for user1
Enter the new value, or press ENTER for the default
        Full Name []: 
        Room Number []: 
        Work Phone []: 
        Home Phone []: 
        Other []: 
Is the information correct? [Y/n] y
```

Next step would be to add that user to the sudoers group to be able to run elevated commands. Just run

```bash
adduser user1 sudo
```

And last, switch over to the new user

```bash
su user1
```

Great, now we are ready to continue

## Get latest updates

```bash
sudo apt update && apt upgrade -y

```

## Change Hostname

```bash
sudo hostnamectl set-hostname <HOSTNAME>
# Also change hostname in /etc/hosts
```

## Set time zone and time

```bash
sudo timedatectyl set-timezone <TIMEZONE>
```

## Install UFW and configure

Install latest version of UFW

```bash
sudo apt install ufw

```

Default allow/deny policies

```bash
# Allow all egress traffic
sudo ufw default allow outgoing
# Deny all ingress traffic
sudo ufw default deny incoming
```

Allow ssh (and http, https, if hosting a website)

```bash
sudo ufw allow ssh

# If needed (can be replaced with port numbers instead)
sudo ufw allow http
sudo ufw allow https

# In addition, if your web server is Nginx
sudo ufw allow "Nginx Full"
```

### Check UFW log file

```bash
grep -i ufw /var/log/syslog
```

Misc. commands

```bash
sudo ufw status
sudo ufw app list | grep <APPLICATION>
sudo ufw status numbered

```

## Install Fail2Ban

Install latest version of Fail2Ban

```bash
sudo apt install fail2ban
```

### Backup config and jail files

```bash
sudo cp /etc/fail2ban/fail2ban.{conf,local}
sudo cp /etc/fail2ban/jail.{conf,local}
```

### Modify the config files if needed

```bash
# Just to make sure loglevel is "INFO" and logtarget is "/var/log/fail2ban.log" 
sudo nano /etc/fail2ban/fail2ban.local
# Can modify bantime, findtime, maxretry, and backend
sudo nano /etc/fail2ban/jail.local
```

### Restart Fail2Ban and check status

```bash
# Restart
sudo systemctl restart fail2ban
# Check status
sudo fail2ban-client status
# Check status of SSH jail
sudo fail2ban-client status sshd
```

### Lastly, to unban an IP

```bash
sudo fail2ban-client set <JAIL> unbanip <IP>
```

## Web Links

Web links to some good resources

[Linode Security Basics](https://www.linode.com/docs/guides/security/basics/)

-eof-