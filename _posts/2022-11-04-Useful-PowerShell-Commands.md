---
title: "Useful PowerShell Commands"
date: 2022-11-04 10:00:00 -700
categories: [Windows]
tags: [network, powershell, cmd, snippet]    #TAG names should always be lowercase
---

# PowerShell Commands

These are some useful PowerShell commands that I find myself using over and over. This list will be updated often

---

## Searching AD for a specific mobile number

> This can be modified to search through other AD user properties also
```powershell
Get-ADUser -filter * -properties DisplayName,mobile | select DisplayName,mobile | Select-String 1234
```

## Get User account information

```powershell
Get-ADUser -Identity <USERNAME> -Properties *
```

## Get Group information

```powershell
Get-ADGroup -Identity <SECURITY_GROUP> -Properties *
```

## Get Group Member in a table format

```powershell
Get-ADGroupMember <GROUP> | FORMAT-Table
```

## Get Group Members, but only their names (or ObjectClass, or DisplyName)

```powershell
Get-ADGroupMember <GROUP> -Recursive | Get-ADUser -Property DisplayName | Select Name,ObjectClass,DisplayName
```

## cmd to get public IP and Country

```powershell
curl ifconfig.io/ip | Out-String -Stream| Select-String "Content"
curl ifconfig.io/country_code | Out-String -Stream| Select-String "Content"
```

## Find Listening ports (22/SSH for example)

```powershell
netstat -ano | select-string "LISTENING"
netstat -ano | select-string ":22"
```

## Find process using that port

> Grab process ID (Last column from previous command)
```powershell
tasklist /fi "PID eq 1528"
```

## Get Domain Controller name

This is the DC your session is currently connected to
```powershell
$env:LogOnServer
```

-eof-