---
title: "Network Snippets"
date: 2022-09-19 15:00:00 -700
categories: [snippet]
tags: [network, powershell, linux, cmd]    #TAG names should always be lowercase
---

# Network Snippets
#### Various snippets of code or one liners that I use often. Stuff that helps in day-2-day troubleshooting and whatnot.
---


### **Linux Commands**

| What | CMD | AltCMD |
|----|----|----|
| Ping Default GW | Ping | Ping |
| Verify IP | ifconfig -a | ip a |
| Verify Link Up | ifconfig -a | ip a |
| Verify Default GW | netstat -rn | ip route |
| Verify Route Table | netstat -rn | ip route |
| Verify ARP Table | arp -an | ip neighbor |

### **Windows Commands**

| What | CMD | |
|----|----|----|
| Ping Default GW | Ping | |
| Verify IP | ipconfig | |
| Verify Link Up | ipconfig | |
| Verify Default GW | netstat -rn | |
| Verify Route Table | netstat -rn | |
| Verify ARP Table | arp -a | |

### Linux cmd to get public IP
```console
curl ifconfig.io/ip
```

### Linux cmd to get public IP continuously
```bash
while true; do curl ifconfig.io/ip; done
```
{: .nolineno }

### PowerShell cmd to get public IP and Country
```powershell
curl ifconfig.io/ip | Out-String -Stream| Select-String "Content"
curl ifconfig.io/country_code | Out-String -Stream| Select-String "Content"
```
{: .nolineno }

### Cisco IOS command to show Assigned and UP interfaces
```console
show ip int br | e unassigned|administratively
```

---
## Working with Git
### Clone existing repo (remote, GitHub) to local server
```console
git clone git@<YOUR-USER-NAME>/<YOUR-REPO-NAME>.git
```

### Push local repo changes to remote GitHub repo
```console
git add .
git commit -m "made some changes"
git push
```
### Sync or Merge remote repo GitHub repo changes back to local repo
```console
git pull
```

