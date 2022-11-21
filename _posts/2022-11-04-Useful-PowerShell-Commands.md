---
title: "Useful PowerShell Commands"
date: 2022-11-04 10:00:00 -700
categories: [snippet]
tags: [network, powershell, cmd]    #TAG names should always be lowercase
---

# PowerShell Commands
These are some useful PowerShell commands that I find myself using over and over

## Searching AD for a specific mobile number
---
This can be modified to search through other AD user properties also
```powershell
Get-ADUser -filter * -properties DisplayName,mobile | select DisplayName,mobile | Select-String 1234
```

## Get User account information
```powershell
Get-ADUser -Identity <USERNAME> -Properties *
```

## cmd to get public IP and Country
```powershell
curl ifconfig.io/ip | Out-String -Stream| Select-String "Content"
curl ifconfig.io/country_code | Out-String -Stream| Select-String "Content"
```

-eof-