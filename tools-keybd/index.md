---
title: "Tools.Keybd"
date: "2017-09-03"
categories: 
  - "vb-net"
tags: 
  - "vb"
---

' An example global keyboard hook is included at the bottom for reference

Namespace Tools
    ''' <summary>
    ''' Simulates keystrokes via a virtual keyboard.
    ''' </summary>
    ''' <remarks>
    ''' Example: Switches to open notepad window and presses Shift+A
    '''    Dim KB As New Tools.Keyboard
    '''    Dim hwnd As Integer = KB.FindWindowHandle("notepad")
    '''    If Not hwnd = 0 Then
    '''       KB.ActivateWindow(hwnd)
    '''       KB.HoldKey(Keys.ShiftKey)
    '''       KB.PressKey(Keys.A)
    '''       KB.ReleaseKey(Keys.ShiftKey)
    '''    End If
    ''' </remarks>
    Public Class Keybd
        Private Declare Function SetForegroundWindow Lib "user32" (ByVal hwnd As Integer) As Integer
        Private Declare Function keybd\_event Lib "user32.dll" (ByVal vk As Byte, ByVal scan As Byte, ByVal flags As Integer, ByVal extrainfo As Integer) As Integer
        Private Const KEYEVENTF\_KEYDOWN = &H0
        Private Const KEYEVENTF\_KEYUP = &H2
 
        ''' <summary>
        ''' Press and release a key on the vKeyboard.
        ''' </summary>
        ''' <param name="key">The key to press.</param>
        ''' <remarks></remarks>
        Public Sub PressKey(ByVal key As Keys)
            HoldKey(key)
            ReleaseKey(key)
        End Sub
 
        ''' <summary>
        ''' Begin holding a key down on the vKeyboard.
        ''' </summary>
        ''' <param name="key">The key to hold.</param>
        ''' <remarks></remarks>
        Public Sub HoldKey(ByVal key As Keys)
            keybd\_event(key, 0, 0, 0)
        End Sub
 
        ''' <summary>
        ''' Stop holding a key down on the vKeyboard.
        ''' </summary>
        ''' <param name="key">The key to release.</param>
        ''' <remarks></remarks>
        Public Sub ReleaseKey(ByVal key As Keys)
            keybd\_event(key, 0, 2, 0)
        End Sub
 
        ''' <summary>
        ''' Bring a window to the foreground to receive keystrokes.
        ''' </summary>
        ''' <param name="hwnd">Handle of the window to bring to the foreground.</param>
        ''' <remarks></remarks>
        Public Sub ActivateWindow(ByVal hwnd As Integer)
            SetForegroundWindow(hwnd)
        End Sub
 
        ''' <summary>
        ''' Does a case insensitive search of all process looking for a window title.
        ''' </summary>
        ''' <param name="PartialTitle">Partial or wrile window caption to search for.</param>
        ''' <returns>The handle of the first window found matching the search criteria.</returns>
        ''' <remarks></remarks>
        Public Function FindWindowHandle(ByVal PartialTitle As String) As Integer
            For Each proc As Process In Process.GetProcesses
                If Not proc.Id = 0 And Not proc.Id = 4 Then
                    Try
                        If proc.MainWindowTitle.ToLower.Contains(PartialTitle.ToLower) Then
                            Return proc.MainWindowHandle
                        End If
                    Catch ex As Exception
                    End Try
                End If
            Next
            Return 0
        End Function
 
        ''' <summary>
        ''' Sends text to the active window delating msDelay miliseconds between keystrokes.
        ''' </summary>
        ''' <param name="text">Text to send.</param>
        ''' <param name="msDelay">Miliseconds to delay between keystrokes.</param>
        ''' <remarks></remarks>
        Public Sub SendText(ByVal text As String, Optional ByVal msDelay As Integer = 10)
            text = text.Replace(vbNewLine, vbCr)
            For Each c As Char In text.ToCharArray
                My.Computer.Keyboard.SendKeys(c.ToString, True)
                If Not msDelay = 0 Then Threading.Thread.Sleep(msDelay)
            Next
        End Sub
    End Class
End Namespace
 
' \*\*\*\*\*\*\*\*\*\*\*\*\*\* GLOBAL HOOK EXAMPLE \*\*\*\*\*\*\*\*\*\*\*\*\*\*
'Public Class Form1
'    'Event types
'    Private Const WM\_KEYUP As Integer = &H101
'    Private Const WM\_KEYDOWN As Short = &H100S
'    Private Const WM\_SYSKEYDOWN As Integer = &H104
'    Private Const WM\_SYSKEYUP As Integer = &H105

'    'Event Info structure
'    Public Structure KBDLLHOOKSTRUCT
'        Public vkCode As Integer 'KeyCode (Of interest to us)
'        Public scanCode As Integer 'ScanCode
'        Public flags As Integer
'        Public time As Integer
'        Public dwExtraInfo As Integer
'    End Structure

'    Enum virtualKey
'        'Misc
'        K\_Return = &HD
'        K\_Backspace = &H8
'        K\_Space = &H20
'        K\_Tab = &H9
'        K\_Esc = &H1B

'        K\_Control = &H11
'        K\_LControl = &HA2
'        K\_RControl = &HA3

'        K\_Delete = &H2E
'        K\_End = &H23
'        K\_Home = &H24
'        K\_Insert = &H2D

'        K\_Shift = &H10
'        K\_LShift = &HA0
'        K\_RShift = &HA1

'        K\_Pause = &H13
'        K\_PrintScreen = 44

'        K\_LWin = &H5B
'        K\_RWin = &H5C

'        K\_Alt = &H12
'        K\_LAlt = &HA4
'        K\_RAlt = &HA5

'        'Locks
'        K\_NumLock = &H90
'        K\_CapsLock = &H14

'        'Arrows
'        K\_Up = &H26
'        K\_Down = &H28
'        K\_Right = &H27
'        K\_Left = &H25

'        'F1-24
'        K\_F1 = &H70
'        K\_F2 = &H71
'        K\_F3 = &H72
'        K\_F4 = &H73
'        K\_F5 = &H74
'        K\_F6 = &H75
'        K\_F7 = &H76
'        K\_F8 = &H77
'        K\_F9 = &H78
'        K\_F10 = &H79
'        K\_F11 = &H7A
'        K\_F12 = &H7B
'        K\_F13 = &H7C
'        K\_F14 = &H7D
'        K\_F15 = &H7E
'        K\_F16 = &H7F
'        K\_F17 = &H80
'        K\_F18 = &H81
'        K\_F19 = &H82
'        K\_F20 = &H83
'        K\_F21 = &H84
'        K\_F22 = &H85
'        K\_F23 = &H86
'        K\_F24 = &H87

'        'Numpad
'        K\_Numpad0 = &H60
'        K\_Numpad1 = &H61
'        K\_Numpad2 = &H62
'        K\_Numpad3 = &H63
'        K\_Numpad4 = &H64
'        K\_Numpad5 = &H65
'        K\_Numpad6 = &H66
'        K\_Numpad7 = &H67
'        K\_Numpad8 = &H68
'        K\_Numpad9 = &H69
'        'Math (numpad)
'        K\_Num\_Add = &H6B
'        K\_Num\_Divide = &H6F
'        K\_Num\_Multiply = &H6A
'        K\_Num\_Subtract = &H6D
'        K\_Num\_Decimal = &H6E

'        'Caracteres e Números
'        K\_0 = &H30
'        K\_1 = &H31
'        K\_2 = &H32
'        K\_3 = &H33
'        K\_4 = &H34
'        K\_5 = &H35
'        K\_6 = &H36
'        K\_7 = &H37
'        K\_8 = &H38
'        K\_9 = &H39
'        K\_A = &H41
'        K\_B = &H42
'        K\_C = &H43
'        K\_D = &H44
'        K\_E = &H45
'        K\_F = &H46
'        K\_G = &H47
'        K\_H = &H48
'        K\_I = &H49
'        K\_J = &H4A
'        K\_K = &H4B
'        K\_L = &H4C
'        K\_M = &H4D
'        K\_N = &H4E
'        K\_O = &H4F
'        K\_P = &H50
'        K\_Q = &H51
'        K\_R = &H52
'        K\_S = &H53
'        K\_T = &H54
'        K\_U = &H55
'        K\_V = &H56
'        K\_W = &H57
'        K\_X = &H58
'        K\_Y = &H59
'        K\_Z = &H5A

'        'Math (not numpad)
'        K\_Subtract = 189
'        K\_Decimal = 190

'    End Enum

'    'Keyboard hook related functions
'    Private Declare Function UnhookWindowsHookEx Lib "user32" (ByVal hHook As Integer) As Integer
'    Private Declare Function SetWindowsHookEx Lib "user32" Alias "SetWindowsHookExA" (ByVal idHook As Integer, ByVal lpfn As KeyboardHookDelegate, ByVal hmod As Integer, ByVal dwThreadId As Integer) As Integer
'    Private Declare Function GetAsyncKeyState Lib "user32" (ByVal vKey As Integer) As Integer
'    Private Declare Function CallNextHookEx Lib "user32" (ByVal hHook As Integer, ByVal nCode As Integer, ByVal wParam As Integer, ByVal lParam As KBDLLHOOKSTRUCT) As Integer
'    Private Delegate Function KeyboardHookDelegate(ByVal Code As Integer, ByVal wParam As Integer, ByRef lParam As KBDLLHOOKSTRUCT) As Integer

'    Private KeyboardHandle As IntPtr = 0 'Handle of the hook
'    Private callback As KeyboardHookDelegate = Nothing 'Delegate for the hook

'    Private Function Hooked()
'        Return KeyboardHandle <> 0 'If KeyboardHandle = 0 it means that it isn't hooked so return false, otherwise return true
'    End Function

'    Public Sub HookKeyboard()
'        callback = New KeyboardHookDelegate(AddressOf KeyboardCallback)
'        KeyboardHandle = SetWindowsHookEx(13, callback, Process.GetCurrentProcess.MainModule.BaseAddress, 0)
'    End Sub

'    Public Sub UnhookKeyboard()
'        If (Hooked()) Then
'            If UnhookWindowsHookEx(KeyboardHandle) <> 0 Then
'                KeyboardHandle = 0 'We have unhooked successfully
'            End If
'        End If
'    End Sub

'    Public Function KeyboardCallback(ByVal Code As Integer, ByVal wParam As Integer, ByRef lParam As KBDLLHOOKSTRUCT) As Integer
'        'If event is KEYDOWN
'        If wParam = WM\_KEYDOWN Or wParam = WM\_SYSKEYDOWN Then
'            If Code >= 0 Then
'                'If Ctrl+Alt+S
'                If My.Computer.Keyboard.CtrlKeyDown And My.Computer.Keyboard.AltKeyDown And lParam.vkCode = virtualKey.K\_S Then
'                    Debug.WriteLine("C+A+S")
'                    Return 1 'Block event
'                End If
'                If lParam.vkCode = virtualKey.K\_Esc Then
'                    Debug.WriteLine("ESC")
'                    Return 1 'Block event
'                End If
'            End If
'        End If
'        Return CallNextHookEx(KeyboardHandle, Code, wParam, lParam) 'Let event go to the other applications
'    End Function

'    Private Sub Form1\_FormClosing(ByVal sender As Object, ByVal e As System.Windows.Forms.FormClosingEventArgs) Handles Me.FormClosing
'        UnhookKeyboard()
'    End Sub

'    Private Sub Form1\_Load(ByVal sender As System.Object, ByVal e As System.EventArgs) Handles MyBase.Load
'        HookKeyboard()
'    End Sub
'End Class
