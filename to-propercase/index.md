---
title: "To-ProperCase"
date: "2017-09-02"
categories: 
  - "powershell"
tags: 
  - "powershell"
---

Add-Type -AssemblyName Microsoft.VisualBasic | Out-Null
 
Function To-ProperCase {
    \[CmdletBinding()\]
    Param (
        \[Parameter(Position=0,Mandatory=$true,ValueFromPipeline=$true)\]
        \[string\]$Text
    )
 
    Process { \[Microsoft.VisualBasic.Strings\]::StrConv($Text, "ProperCase") }
}
