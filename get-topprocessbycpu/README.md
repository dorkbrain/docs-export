---
title: "Get-TopProcessByCPU"
date: "2017-09-02"
categories: 
  - "powershell"
tags: 
  - "powershell"
---

Function Get-TopProcessByCPU ( \[int\]$First=999, \[string\]$ComputerName="localhost" ) {
<#
 
.DESCRIPTION
 
Returns a list of running processes sorted by % CPU used.
 
.PARAMETER First
 
Return the top # of processes.
 
.PARAMETER ComputerName
 
Get processes from a remote computer.
 
.EXAMPLE
 
Get-TopProcessByCPU 10
Returns the top 10 processes from the local computer.
 
.EXAMPLE
 
Get-TopProcessByCPU -First 5 -ComputerName RemoteHost
Returns the top 5 processes on the computer named 'RemoteHost'.
 
#>
 
  Invoke-Command -ComputerName $ComputerName {
    Param(\[int\]$First)
    $script:ErrorActionPreference = "SilentlyContinue"
    $results = @()
    $tableFormat = @{Expression={$Env:COMPUTERNAME};Label="ComputerName";Width=(($Env:COMPUTERNAME).Length+2)}, \`
                   @{Expression={$\_.InstanceName};Label="Process Name";width=30}, \`
                   @{Expression={"{0:N2}" -f $\_.CookedValue};Label="%CPU";width=8}
    $counterData = (Get-Counter "\\Process(\*)\\% Processor Time").CounterSamples | Where-Object { $\_.InstanceName -ne "\_total" -and $\_.InstanceName -ne "Idle" } | Sort-Object -Property CookedValue -Descending
    $counterData | Select-Object -First $First | ForEach-Object {
      $props = @{
        ProcessName = $\_.InstanceName
        ComputerName = $Env:COMPUTERNAME
        ProcessorPercentage = $\_.CookedValue
      }
      $results += New-Object PSObject -Property $props
    }
    $results
  } -ArgumentList $First -HideComputerName
}
