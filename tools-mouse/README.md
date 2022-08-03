---
title: "Tools.Mouse"
date: "2017-09-03"
categories: 
  - "vb-net"
tags: 
  - "vb"
---

Namespace Tools
    Public Class Mouse
        Private Declare Function SetCursorPos Lib "user32" (ByVal x As Integer, ByVal y As Integer) As Integer
        Private Declare Function GetCursorPos Lib "user32" (ByRef lpPoint As Point) As Integer
        Private Declare Sub mouse\_event Lib "user32" Alias "mouse\_event" (ByVal dwFlags As MouseFlags, ByVal dx As Integer, ByVal dy As Integer, ByVal cButtons As Integer, ByVal dwExtraInfo As Integer)
        Private WithEvents MouseMon As New Timer
 
        Private Enum MouseFlags As Integer
            LEFTDOWN = &H2
            LEFTUP = &H4
            MIDDLEDOWN = &H20
            MIDDLEUP = &H40
            RIGHTDOWN = &H8
            RIGHTUP = &H10
        End Enum
 
        Public Structure ButtonState
            Shared Left As Boolean = False
            Shared Middle As Boolean = False
            Shared Right As Boolean = False
            Shared X1 As Boolean = False
            Shared X2 As Boolean = False
        End Structure
 
        Public Sub LeftDown()
            mouse\_event(MouseFlags.LEFTDOWN, 0, 0, 0, 0)
        End Sub
        Public Sub LeftUp()
            mouse\_event(MouseFlags.LEFTUP, 0, 0, 0, 0)
        End Sub
        Public Sub LeftClick()
            LeftDown()
            LeftUp()
        End Sub
 
        Public Sub MiddleDown()
            mouse\_event(MouseFlags.MIDDLEDOWN, 0, 0, 0, 0)
        End Sub
        Public Sub MiddleUp()
            mouse\_event(MouseFlags.MIDDLEUP, 0, 0, 0, 0)
        End Sub
        Public Sub MiddleClick()
            MiddleDown()
            MiddleUp()
        End Sub
 
        Public Sub RightDown()
            mouse\_event(MouseFlags.RIGHTDOWN, 0, 0, 0, 0)
        End Sub
        Public Sub RightUp()
            mouse\_event(MouseFlags.RIGHTUP, 0, 0, 0, 0)
        End Sub
        Public Sub RightClick()
            RightDown()
            RightUp()
        End Sub
 
        Public Function GetPosition() As Point
            Dim tempPoint As New Point
            GetCursorPos(tempPoint)
            Return tempPoint
        End Function
 
        Public Sub SetPosition(ByVal newPos As Point)
            SetPosition(newPos.X, newPos.Y)
        End Sub
        Public Sub SetPosition(ByVal x As Integer, ByVal y As Integer)
            SetCursorPos(x, y)
        End Sub
 
        Private Sub MouseMon\_Tick(ByVal sender As Object, ByVal e As System.EventArgs) Handles MouseMon.Tick
            If Control.MouseButtons And MouseButtons.Left Then ButtonState.Left = True Else ButtonState.Left = False
            If Control.MouseButtons And MouseButtons.Middle Then ButtonState.Middle = True Else ButtonState.Middle = False
            If Control.MouseButtons And MouseButtons.Right Then ButtonState.Right = True Else ButtonState.Right = False
            If Control.MouseButtons And MouseButtons.XButton1 Then ButtonState.X1 = True Else ButtonState.X1 = False
            If Control.MouseButtons And MouseButtons.XButton2 Then ButtonState.X2 = True Else ButtonState.X2 = False
        End Sub
 
        Public Sub New()
            MouseMon.Interval = 1
            MouseMon.Enabled = True
        End Sub
    End Class
End Namespace
