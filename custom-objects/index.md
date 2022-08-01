---
title: "Custom Objects"
date: "2017-09-02"
categories: 
  - "powershell"
tags: 
  - "powershell"
---

Standard custom object:

\[PSCustomObject\]@{
  Name = "winmgmt"
  Desc = "WinRM Management"
}

 

Ordered custom object:

\[PSCustomObject\]\[ordered\]@{
  Desc = "WinRM Management"
  Name = "winmgmt"
}

 

\[PSCustomObject\] casting is available in PowerShell v3 and up. For v2 use the below method to avoid creating a hashtable instead.

New-Object PSCustomObject -Property @{
  Desc = "WinRM Management"
  Name = "winmgmt"
}

 

\[ordered\] is also only available in v3 and up. To order a PSCustomObject in v2 try this.

New-Object PSCustomObject -Property @{
  Desc = "WinRM Management"
  Name = "winmgmt"
} | Select-Object Desc, Name
