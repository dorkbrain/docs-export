---
title: "Profile Paths"
date: "2017-09-03"
categories: 
  - "powershell"
tags: 
  - "powershell"
---

 

| **User Context** | **Host Context** | **Variable** | **Path** |
| :-- | :-- | :-- | :-- |
| Current | Current | $Profile.CurrentUserCurrentHost | $Home\\Documents\\WindowsPowerShell\\Profile.ps1 |
| Current | All | $Profile.CurrentUserAllHosts | $Home\\Documents\\Profile.ps1 |
| All | Current | $Profile.AllUsersCurrentHost | $PSHome\\Microsoft.PowerShell\_profile.ps1 |
| All | All | $Profile.AllUsersAllHosts | $PSHome\\Profile.ps1 |
| Current | Current: ISE |  | $Home\\Documents\\WindowsPowerShell\\Microsoft.PowerShellISE\_profile.ps1 |
| All | Current: ISE |  | $PsHome\\Microsoft.PowerShellISE\_profile.ps1 |

The one-liner below will list all non-ISE profile paths in your current session:

$Profile | Get-Member -MemberType NoteProperty | Select-Object Name, Definition
