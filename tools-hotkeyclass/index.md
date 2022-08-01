---
title: "Tools.HotKeyClass"
date: "2017-09-03"
categories: 
  - "vb-net"
tags: 
  - "vb"
---

Namespace Tools
  Public Class HotkeyClass
      Private Declare Function RegisterHotKey Lib "user32" (ByVal hwnd As IntPtr, ByVal id As Integer, ByVal fsModifiers As Integer, ByVal vk As Integer) As Boolean
      Private Declare Function UnregisterHotKey Lib "user32" (ByVal hwnd As IntPtr, ByVal id As Integer) As Boolean
      Public Const WM\_HOTKEY As Integer = &H312
 
      Public Enum KeyModifier
          None = &H0
          Alt = &H1
          Control = &H2
          Shift = &H4
          Winkey = &H8
      End Enum
 
      Public Structure HotKeyInfo
          Dim HotKeyID As Integer
          Dim TriggerKey As String
          Dim Modifier As KeyModifier
      End Structure
 
      Private RegisteredHotKeys As New List(Of HotKeyInfo)
 
      Public Function Register(ByRef sourceForm As Form, ByVal triggerKey As String, ByVal modifier As KeyModifier) As Integer
          Dim hkiNew As New HotKeyInfo
          hkiNew.HotKeyID = RegisteredHotKeys.Count + 1
          hkiNew.TriggerKey = triggerKey
          hkiNew.Modifier = modifier
 
          If RegisterHotKey(sourceForm.Handle, 1, modifier, Asc(triggerKey.ToUpper)) = False Then
              Throw New Exception("HotKey registration failed.  Perhaps the key combination is reserved by the system or used by another program.")
          Else
              RegisteredHotKeys.Add(hkiNew)
              Return hkiNew.HotKeyID
          End If
      End Function
 
      Public Sub Unregister(ByRef sourceForm As Form)
          UnregisterHotKey(sourceForm.Handle, 1)
      End Sub
 
      Public Function HandleWndProc(m As System.Windows.Forms.Message) As HotKeyInfo
          Dim hotkeyID As Integer = m.WParam.ToInt32
          For Each thisHKI As HotKeyInfo In RegisteredHotKeys
              If hotkeyID = thisHKI.HotKeyID Then Return thisHKI
          Next
          Return Nothing
      End Function
  End Class
End Namespace
