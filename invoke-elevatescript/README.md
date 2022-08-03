---
title: "Invoke-ElevateScript"
date: "2017-09-02"
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
