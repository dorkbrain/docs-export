---
title: "Tools.MonitorPower"
date: "2017-09-03"
categories: 
  - "vb-net"
tags: 
  - "vb"
---

Imports System.Runtime.InteropServices
 
Namespace Tools
  Public Class MonitorPower
      <DllImport("user32.dll", SetLastError:=True, CharSet:=CharSet.Auto)> \_
      Private Shared Function SendMessage(ByVal hWnd As Integer, ByVal Msg As Integer, ByVal wParam As Integer, ByVal lParam As Integer) As Integer
      End Function
 
      Private Const HWND\_BROADCAST As Integer = &HFFFF
      Dim WM\_SYSCOMMAND As Integer = &H112
      Dim SC\_MONITORPOWER As Integer = &HF170
      Dim MON\_ON As Integer = -1
      Dim MON\_OFF As Integer = 2
 
      Public Sub TurnOn(callingForm As Form)
          Debug.WriteLine("MonitorPowerClass.TurnOn called")
          SendMessage(callingForm.Handle.ToInt32, WM\_SYSCOMMAND, SC\_MONITORPOWER, MON\_ON)
      End Sub
 
      Public Sub TurnOff(callingForm As Form)
          Debug.WriteLine("MonitorPowerClass.TurnOff called")
          SendMessage(callingForm.Handle.ToInt32, WM\_SYSCOMMAND, SC\_MONITORPOWER, MON\_OFF)
      End Sub
  End Class
End Namespace
