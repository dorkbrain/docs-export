---
title: "Invoke-MonitorPower"
date: "2017-09-02"
categories: 
  - "powershell"
tags: 
  - "powershell"
---

Function Invoke-MonitorPower {
    <#
        .SYNOPSIS
            Turns a monitor on or off
 
        .DESCRIPTION
            Turns a monitor on or off
 
        .PARAMETER PowerOp
            Power operation: On or Off
 
        .EXAMPLE
            Invoke-MonitorPower -PowerOp On
            Turns the monitor on
 
        .EXAMPLE
            Invoke-MonitorPower -PowerOp Off
            Turns the monitor off
 
        .EXAMPLE
            Invoke-MonitorPower -PowerOp Off; Start-Sleep -Seconds 2; Invoke-MonitorPower -PowerOp On
            Turns the monitor off, pauses 2 seconds, then turns it back on
    #>
    \[cmdletbinding()\]
    Param (
        \[Parameter(Mandatory=$true)\]
        \[ValidateSet("On", "Off")\]
        \[string\]$PowerOp
    )
    Begin {        
        Try {
            # Check to see if the PowerMonitor class has been loaded.
            $null = \[MonitorPower\]
        } Catch {
            # It hasn't been loaded yet, do so now.
            $code = @"
              Imports System
              Imports System.Runtime.InteropServices
 
              Public Class MonitorPower
                <DllImport("user32.dll", SetLastError:=True, CharSet:=CharSet.Auto)> \_
                Private Shared Function SendMessage(ByVal hWnd As Integer, \_
                  ByVal Msg As Integer, \_
                  ByVal wParam As Integer, \_
                  ByVal lParam As Integer) As Integer
                End Function
 
                Private Const HWND\_BROADCAST As Integer = &HFFFF
                Private Const WM\_SYSCOMMAND As Integer = &H112
                Private Const SC\_MONITORPOWER As Integer = &HF170
                Private Const MON\_ON As Integer = -1
                Private Const MON\_OFF As Integer = 2
 
                Public Shared Sub TurnOn()
                  SendMessage(HWND\_BROADCAST, WM\_SYSCOMMAND, SC\_MONITORPOWER, MON\_ON)
                End Sub
 
                Shared Sub TurnOff()
                  SendMessage(HWND\_BROADCAST, WM\_SYSCOMMAND, SC\_MONITORPOWER, MON\_OFF)
                End Sub
              End Class
"@
          Add-Type -Language VisualBasic -TypeDefinition $code
        }
    }
    Process {
        Switch($PowerOp) {
          "On" { \[MonitorPower\]::TurnOn() }
          "Off" { \[MonitorPower\]::TurnOff() }        
        }
    }
}
Set-Alias imp Invoke-MonitorPower
