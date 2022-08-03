---
title: "Test-Port"
date: "2017-09-02"
categories: 
  - "powershell"
tags: 
  - "powershell"
---

Function Test-Port {
  <#
    .DESCRIPTION
      Tests if a computer is responding to icmp and if an individual port is open.
 
    .PARAMETER ComputerName
      The name of the computer to test.
 
    .PARAMETER Port
      The port to test on the remote computer.
 
    .PARAMETER Interval
      Repeat port test every x seconds.
 
    .EXAMPLE
      Test-Port -ComputerName server1 -Port 3389
      Tests if 'server1' is up and if it's responding to remote desktop requests.
 
    .EXAMPLE
      Test-Port -ComputerName server1 -Port 3389 -Interval 3
      Tests if 'server1' is up and if it's responding to remote desktop requests.  This test repeats every 3 seconds until stopped with Ctrl-C.
  #>
 
  \[CmdletBinding()\]
  Param (
    \[Parameter(Position=0,Mandatory=$true,ValueFromPipeline=$true)\]
    \[string\]$ComputerName,
    \[Parameter(Position=1,Mandatory=$true,ValueFromPipeline=$false)\]
    \[int\]$Port,
    \[Parameter(Position=2,Mandatory=$false,ValueFromPipeline=$false)\]
    \[int\]$Interval = 0
  )
 
  Begin { }
 
  Process {
    If ($interval -gt 0) {
      Do {
        & $MyInvocation.MyCommand -ComputerName $ComputerName -Port $Port
      } Until ( Start-Sleep -Seconds $interval )
    } else {
      $thisHost = $ComputerName
      $thisPort = $Port
      $icmpUp = $False
      $portUp = $False
 
      If ( (Test-Connection $thisHost -Count 1 -ErrorAction SilentlyContinue) ) {
        $icmpUp = $true
      } Else {
        $icmpUp = $false
      }
 
      Try {
        $socket = New-Object System.Net.Sockets.TcpClient($thisHost, $thisPort)
        $socket.Close()
        $portUp = $true
      } Catch {
        $portUp = $false
      }
 
      $props = @{
      TimeStamp = (Get-Date).ToLongTimeString()
        Name = $thisHost
        Ping = $icmpUp
        Port = $portUp
      }
      New-Object PSObject -Property $props
    }
  }
 
  End { }
}
