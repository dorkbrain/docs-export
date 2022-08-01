---
title: "Windows System Idle Timer"
date: "2017-09-03"
categories: 
  - "powershell"
tags: 
  - "powershell"
---

Here's the VB.Net class that does the API work:

$typeDef = @"
Imports System
Imports System.Runtime.InteropServices

Public Class WinAPI
    ' https://msdn.microsoft.com/en-us/library/aa373208(v=vs.85).aspx
    ' Functions and Subs declared as Public Shared so they can be called
    ' directly instead of having to create a new instance of the class.

    'Value                          Meaning
    '----------------------------- -------------------------------------------
    'ES\_AWAYMODE\_REQUIRED           Enables away mode. This value must be 
    '0x00000040                     specified with ES\_CONTINUOUS.  Away mode 
    '                               should be used only by media-recording and
    '                               media-distribution applications that must 
    '                               perform critical background processing on 
    '                               desktop computers while the computer 
    '                               appears to be sleeping. See Remarks.
    '
    '                               Windows Server 2003:  ES\_AWAYMODE\_REQUIRED 
    '                               is not supported.
    '
    'ES\_CONTINUOUS                  Informs the system that the state being set
    '0x80000000                     should remain in effect until the next call
    '                               that uses ES\_CONTINUOUS and one of the 
    '                               other state flags is cleared.
    '
    'ES\_DISPLAY\_REQUIRED            Forces the display to be on by resetting 
    '0x00000002                     the display idle timer.
    '
    '                               Windows 8:  This flag can only keep a 
    '                               display turned on, it can't turn on a 
    '                               display that's currently off.
    '
    'ES\_SYSTEM\_REQUIRED             Forces the system to be in the working 
    '0x00000001                     state by resetting the system idle timer.
    '
    'ES\_USER\_PRESENT                This value is not supported. If 
    '0x00000004                     ES\_USER\_PRESENT is combined with other 
    '                               esFlags values, the call will fail and none
    '                               of the specified states will be set.
    '
    '                               Windows Server 2003:  Informs the system 
    '                               that a user is present and resets the 
    '                               display and system idle timers. 
    '                               ES\_USER\_PRESENT must be called with 
    '                               ES\_CONTINUOUS.

    <FlagsAttribute> \_
    Public Enum EXECUTION\_STATE As UInteger
        ES\_AWAYMODE\_REQUIRED = &H40
        ES\_CONTINUOUS = &H80000000UI
        ES\_DISPLAY\_REQUIRED = &H2
        ES\_SYSTEM\_REQUIRED = &H1
        ES\_USER\_PRESENT = &H4 ' Do not use, here for legacy support
    End Enum

    <DllImport("kernel32.dll", CharSet:=CharSet.Auto, SetLastError:=True)> \_
    Public Shared Function SetThreadExecutionState(esFlags As EXECUTION\_STATE) As EXECUTION\_STATE
    End Function

    Public Shared Sub PreventMonitorPowerdown()
        SetThreadExecutionState(EXECUTION\_STATE.ES\_DISPLAY\_REQUIRED Or EXECUTION\_STATE.ES\_CONTINUOUS)
    End Sub

    Public Shared Sub AllowMonitorPowerdown()
        SetThreadExecutionState(EXECUTION\_STATE.ES\_CONTINUOUS)
    End Sub

    Public Shared Sub PreventSleep()
        ' Prevent Idle-to-Sleep (monitor not affected)
        SetThreadExecutionState(EXECUTION\_STATE.ES\_CONTINUOUS Or EXECUTION\_STATE.ES\_AWAYMODE\_REQUIRED)
    End Sub

    Public Shared Sub KeepSystemAwake()
        SetThreadExecutionState(EXECUTION\_STATE.ES\_SYSTEM\_REQUIRED)
    End Sub

    <StructLayout(LayoutKind.Sequential)> \_
    Public Structure LASTINPUTINFO
        <MarshalAs(UnmanagedType.U4)> \_
        Public cbSize As Integer
        <MarshalAs(UnmanagedType.U4)> \_
        Public dwTime As Integer
    End Structure

    <DllImport("user32.dll")> \_
    Public Shared Function GetLastInputInfo(ByRef plii As LASTINPUTINFO) As Boolean
    End Function

    Public Shared Function GetLastInputTime() As Integer
        Dim idletime As Integer = 0
        Dim lastInputInf As New LASTINPUTINFO
        lastInputInf.cbSize = Marshal.SizeOf(lastInputInf)
        lastInputInf.dwTime = 0

        If GetLastInputInfo(lastInputInf) Then
            idletime = Environment.TickCount - lastInputInf.dwTime
        End If

        If idletime > 0 Then
            Return idletime / 1000
        Else
            Return 0
        End If
    End Function
End Class
"@

Add-Type -Language VisualBasic -TypeDefinition $typeDef

 

To keep the system from idling to sleep:

\[WinAPI\]::KeepSystemAwake()

 

To show the current system idle timer:

While (-Not (Start-Sleep -Seconds 1) ) {
    "Idle Timer: {0} seconds" -f \[WinAPI\]::GetLastInputTime()
}

 

This code only affects the current thread. As soon as the thread closes the system is returned to its previous state.
