---
title: "Watch-Script"
date: "2017-09-02"
categories: 
  - "powershell"
tags: 
  - "powershell"
---

Function Watch-Script (\[int\]$Delay = 3, \[int\]$Iterations = -1, \[scriptblock\]$ScriptBlock ) {
<#
 
.DESCRIPTION
 
Repeats the same block of code a certain number of times with an optional delay.  Attempting to mimic the linux 'watch' command.
 
.PARAMETER Delay
 
Delay # seconds between iterations
 
.PARAMETER Iterations
 
Number of times to repeat the script.  -1 means repeat forever.
 
.PARAMETER ScriptBlock
 
Insert a block of code between the curly braces to execute.
 
.EXAMPLE
 
Watch-Script 5 10 { Get-Process | Sort-Object ProcessName }
Gets a list of processes sorted by name every 5 seconds.  It will repeat 10 times.
 
.EXAMPLE
 
Watch-Script 5 -1 { Get-Process | Sort-Object ProcessName }
Gets a list of processes sorted by name every 5 seconds.  It will repeat forever, or until Ctrl-C is pressed.
 
#>
  $count = 0
  While ($count -ne $Iterations) {
    If ($Iterations -gt 0) { $count += 1 }
    $ScriptBlock.Invoke()
    If ($count -ne $Iterations) { Start-Sleep $Delay }
  }  
}
