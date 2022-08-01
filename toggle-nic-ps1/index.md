---
title: "Toggle-NIC.ps1"
date: "2017-09-03"
categories: 
  - "powershell"
tags: 
  - "powershell"
---

Function Invoke-ElevateScript {
<#
.SYNOPSIS
Re-launches the current script as administrator
  
.DESCRIPTION
Achieves UAC elevation by re-launching the current script as administrator.
  
.PARAMETER ScriptBlock
Block of code to execute as administrator.  If this is omitted the current script file is assumed.
  
.PARAMETER SkipCheck
Assume this user context isn't elevated and always re-launch
  
.EXAMPLE
Invoke-ElevateScript
  
If the current script isn't running in an elevated context then re-launch as administrator
  
.EXAMPLE
Invoke-ElevateScript -SkipCheck
  
Re-launch as administrator regardless of the current context
#>
  param(
    \[ScriptBlock\]$ScriptBlock = {},
    \[Switch\]$SkipCheck
  )
 
  \[boolean\]$isAdmin = $false
  If (-not $SkipCheck) {
    $isAdmin = (\[Security.Principal.WindowsPrincipal\]\[Security.Principal.WindowsIdentity\]::GetCurrent()).IsInRole(\[Security.Principal.WindowsBuiltInRole\] "Administrator")
  }
 
  If (-not $isAdmin) {
    If ($ScriptBlock.ToString()) {
      Start-Process $PSHOME\\powershell.exe -Verb RunAs -ArgumentList "-command \`"$($ScriptBlock)\`""
      Break
    } Else {
      If (-not $MyInvocation.ScriptName) {
        Start-Process $PSHOME\\powershell.exe -Verb RunAs -ArgumentList $myInvocation.MyCommand.Definition
        Break
      } Else {
        Start-Process $PSHOME\\powershell.exe -Verb RunAs -ArgumentList "-file \`"$($MyInvocation.ScriptName)\`""
        Break
      }
    }
  }
}
Invoke-ElevateScript

$did=$true
while ($did) {
  Get-WmiObject -Class Win32\_NetworkAdapter -Filter "PhysicalAdapter = 'True'" | Select-Object DeviceID, Name, NetEnabled | Format-Table -Auto
  $did = Read-Host -Prompt "Select a DeviceID to toggle \[ENTER to exit\]"
  if ($did) {
    $wmi = Get-WmiObject -Class Win32\_NetworkAdapter -Filter "DeviceID = $did"
    if ($wmi.PhysicalAdapter) {
      $nicName = $wmi.Name
      if ($wmi.NetEnabled) {
        "Disabling $nicName"
        try {
          \[void\] $wmi.disable()
        } catch {
          "Failed to disable adapter!"
        }
      } else {
        "Enabling $nicName"
        try {
          \[void\] $wmi.enable()
        } catch {
          "Failed to enable adapter!"
        }
      }
    } else {
      ""
      ""
      "Please select a physical adapter from the list!"
      ""
      ""
    }
  }
}
