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

## View all locked accounts

```powershell
Search-ADAccount -LockedOut | Select-Object Name, SamAccountName, LockedOut, LastLogonDate, DistinguishedName
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

## Running Active Directory cmdlets as another user

If you need to run AD cmdlets as another user - perhaps your admin account - you can do the following

```powershell
$admin = Get-Credential
```

This will pop-up a dialog box so you can input your desired user credentials. This will be saved under the $admin alias (can be changed to whatever you want)

Then you can run your desired commands like this

```powershell
Set-ADAccountExpiration -Credential $admin -Identity john.doe -DateTime "03/11/2029"
```

## Changing A User Password

```powershell
Set-ADAccountPassword -Identity john.doe -Reset -NewPassword (ConvertTo-SecureString -AsPlainText "SuperCoolNewPassword" -Force)
```

## Get a list of GPOs installed

```powershell
Get-GPO -All
```

## Export GPO

```powershell
Get-GPOReport -Name "SetWallpaper" -ReportType HTML -Path ".\SetWallpaper.html"
```

## List GPOs that were recently modified

```powershell
Get-GPO -All | Where-Object { $_.ModificationTime } | Select-Object DisplayName,ModificationTime
```

Example

```powershell
Get-GPO -All | Where-Object { $_.ModificationTime } | Select-Object DisplayName, ModificationTime

DisplayName                                ModificationTime
-----------                                ----------------
Default Domain Policy                      10/14/2024 12:19:28 PM
Default Domain Controllers Policy          10/14/2024 12:17:30 PM
SetWallpaper                               10/31/2024 1:01:04 PM
```

## Check Group membership, across groups

Useful one-line to check and see if members of <group1> are also members of <group2> (in this case, using a wildcard for the second group). And then only show the info I care about

```powershell
Get-ADGroupMember "group1" | Where-Object {(Get-ADUser $_.SamAccountName -Properties MemberOf).MemberOf -match "CN=.*VPN.*,"} | Select-Object SamAccountName, Name, DistinguishedName

SamAccountName    Name                 DistinguishedName
--------------    ----                 -----------------
user01          user01              CN=user01,OU=Users,OU=NA,DC=something,DC=com
user02          user02              CN=user02,OU=Users,OU=NA,DC=something,DC=com
user03          user03              CN=user03,OU=Users,OU=NA,DC=something,DC=com
```

## List out the groups that a user is a member of

When you need to get a nice clean list of the groups that a user is a member of

```powershell
Get-ADUser -Identity <userName> -Property MemberOf | Select-Object -ExpandProperty MemberOf

CN=Domain Users,OU=Groups,OU=North America,DC=company,DC=com
CN=Cool Users,OU=Groups,OU=North America,DC=company,DC=com
CN=Dept-FunTimes,OU=Groups,OU=North America,DC=company,DC=com
```

## Unquoted Service Paths

A way to find services with unquoted service paths. A local attacker can gain elevated privileges by inserting an executable file in the path of the affected service.

[Context](https://blackpointcyber.com/blog/unlocking-the-mystery-of-unquoted-service-paths-another-opportunity-for-privilege-escalation/)


```powershell
Get-WmiObject win32_service | select Name,PathName,StartMode,StartName | where {$_.StartMode -ne "Disabled" -and $_.StartName -eq "LocalSystem" -and $_.PathName -notmatch "`"" -and $_.PathName -notmatch "C:\\Windows"} | Format-List
```

-eof-