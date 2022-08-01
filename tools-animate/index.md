---
title: "Tools.Animate"
date: "2017-09-03"
categories: 
  - "vb-net"
tags: 
  - "vb"
---

Imports System.Runtime.InteropServices
 
Namespace Tools
    Public Class Animate
        ' Use the API to ensure double-buffering
        <DllImport("user32.dll", SetLastError:=True)> \_
        Private Shared Function MoveWindow(ByVal hWnd As IntPtr, ByVal X As Integer, ByVal Y As Integer, ByVal nWidth As Integer, ByVal nHeight As Integer, ByVal bRepaint As Boolean) As Boolean
        End Function
 
        <DllImport("user32.dll")> \_
        Private Shared Function GetWindowRect(ByVal hWnd As IntPtr, ByRef lpRect As RECT) As Boolean
        End Function
 
        <StructLayout(LayoutKind.Sequential)> \_
        Private Structure RECT
            Private \_Left As Integer, \_Top As Integer, \_Right As Integer, \_Bottom As Integer
 
            Public Sub New(ByVal Rectangle As Rectangle)
                Me.New(Rectangle.Left, Rectangle.Top, Rectangle.Right, Rectangle.Bottom)
            End Sub
            Public Sub New(ByVal Left As Integer, ByVal Top As Integer, ByVal Right As Integer, ByVal Bottom As Integer)
                \_Left = Left
                \_Top = Top
                \_Right = Right
                \_Bottom = Bottom
            End Sub
 
            Public Property X As Integer
                Get
                    Return \_Left
                End Get
                Set(ByVal value As Integer)
                    \_Left = value
                End Set
            End Property
            Public Property Y As Integer
                Get
                    Return \_Top
                End Get
                Set(ByVal value As Integer)
                    \_Top = value
                End Set
            End Property
            Public Property Left As Integer
                Get
                    Return \_Left
                End Get
                Set(ByVal value As Integer)
                    \_Left = value
                End Set
            End Property
            Public Property Top As Integer
                Get
                    Return \_Top
                End Get
                Set(ByVal value As Integer)
                    \_Top = value
                End Set
            End Property
            Public Property Right As Integer
                Get
                    Return \_Right
                End Get
                Set(ByVal value As Integer)
                    \_Right = value
                End Set
            End Property
            Public Property Bottom As Integer
                Get
                    Return \_Bottom
                End Get
                Set(ByVal value As Integer)
                    \_Bottom = value
                End Set
            End Property
            Public Property Height() As Integer
                Get
                    Return \_Bottom - \_Top
                End Get
                Set(ByVal value As Integer)
                    \_Bottom = value - \_Top
                End Set
            End Property
            Public Property Width() As Integer
                Get
                    Return \_Right - \_Left
                End Get
                Set(ByVal value As Integer)
                    \_Right = value + \_Left
                End Set
            End Property
            Public Property Location() As Point
                Get
                    Return New Point(Left, Top)
                End Get
                Set(ByVal value As Point)
                    \_Left = value.X
                    \_Top = value.Y
                End Set
            End Property
            Public Property Size() As Size
                Get
                    Return New Size(Width, Height)
                End Get
                Set(ByVal value As Size)
                    \_Right = value.Width + \_Left
                    \_Bottom = value.Height + \_Top
                End Set
            End Property
 
            Public Shared Widening Operator CType(ByVal Rectangle As RECT) As Rectangle
                Return New Rectangle(Rectangle.Left, Rectangle.Top, Rectangle.Width, Rectangle.Height)
            End Operator
            Public Shared Widening Operator CType(ByVal Rectangle As Rectangle) As RECT
                Return New RECT(Rectangle.Left, Rectangle.Top, Rectangle.Right, Rectangle.Bottom)
            End Operator
            Public Shared Operator =(ByVal Rectangle1 As RECT, ByVal Rectangle2 As RECT) As Boolean
                Return Rectangle1.Equals(Rectangle2)
            End Operator
            Public Shared Operator <>(ByVal Rectangle1 As RECT, ByVal Rectangle2 As RECT) As Boolean
                Return Not Rectangle1.Equals(Rectangle2)
            End Operator
 
            Public Overrides Function ToString() As String
                Return "{Left: " & \_Left & "; " & "Top: " & \_Top & "; Right: " & \_Right & "; Bottom: " & \_Bottom & "}"
            End Function
 
            Public Overloads Function Equals(ByVal Rectangle As RECT) As Boolean
                Return Rectangle.Left = \_Left AndAlso Rectangle.Top = \_Top AndAlso Rectangle.Right = \_Right AndAlso Rectangle.Bottom = \_Bottom
            End Function
            Public Overloads Overrides Function Equals(ByVal \[Object\] As Object) As Boolean
                If TypeOf \[Object\] Is RECT Then
                    Return Equals(DirectCast(\[Object\], RECT))
                ElseIf TypeOf \[Object\] Is Rectangle Then
                    Return Equals(New RECT(DirectCast(\[Object\], Rectangle)))
                End If
 
                Return False
            End Function
        End Structure
 
        ' Timers used to facilitate the animation
        Private Shared animateX As New Timer
        Private Shared animateY As New Timer
        Private Shared animateW As New Timer
        Private Shared animateH As New Timer
 
        ' Variables to back the public properties
        Private Shared m\_Duration As Integer = 25
        Private Shared m\_Speed As Integer = 1
 
        ' Variables used to back the animation timers
        Private Shared stepX, stepY, stepW, stepH As Double
        Private Shared newX, newY, newW, newH As Double
 
        ''' <summary>
        ''' The higher the duration the slower and smoother the animation
        ''' </summary>
        Public Shared Property Duration As Integer
            Get
                Return m\_Duration
            End Get
            Set(value As Integer)
                m\_Duration = value
            End Set
        End Property
 
        ''' <summary>
        ''' The higher the speed the slower the animation
        ''' </summary>
        Public Shared Property Speed As Integer
            Get
                Return m\_Speed
            End Get
            Set(value As Integer)
                m\_Speed = value
            End Set
        End Property
 
        ''' <summary>
        ''' Method to animate the movement and/or resizing of an object.
        ''' </summary>
        ''' <param name="NewX">New X location of the object.</param>
        ''' <param name="NewY">New Y location of the object.</param>
        ''' <param name="NewWidth">New width of the object.</param>
        ''' <param name="NewHeight">New height of the object.</param>
        ''' <param name="SourceHandle">Handle to the object to animate.</param>
        ''' <remarks></remarks>
        Public Shared Sub AnimateObject(NewX As Integer, NewY As Integer, NewWidth As Integer, NewHeight As Integer, SourceHandle As IntPtr)
            AnimateObject(New Point(NewX, NewY), New Size(NewWidth, NewHeight), SourceHandle)
        End Sub
 
        ''' <summary>
        ''' Method to animate the movement and/or resizing of an object.
        ''' </summary>
        ''' <param name="NewPosition">New position of the object.</param>
        ''' <param name="NewSize">New size of the object.</param>
        ''' <param name="SourceHandle">Handle to the object to animate.</param>
        ''' <remarks></remarks>
        Public Shared Sub AnimateObject(NewPosition As Point, NewSize As Size, SourceHandle As IntPtr)
            AnimateObject(New Rectangle(NewPosition, Newsize), SourceHandle)
        End Sub
 
        ''' <summary>
        ''' Method to animate the movement and/or resizing of an object.
        ''' </summary>
        ''' <param name="NewWindowRect">New size and position of the object.</param>
        ''' <param name="SourceHandle">Handle to the object to animate.</param>
        ''' <remarks></remarks>
        Public Shared Sub AnimateObject(NewWindowRect As Rectangle, SourceHandle As IntPtr)
            ' How many timer ticks will it take to animate the object
            Dim totalTicks As Integer = m\_Duration / m\_Speed
 
            ' Get the initial size of the object
            Dim sourceRect As New RECT
            GetWindowRect(SourceHandle, sourceRect)
            newX = sourceRect.Left
            newY = sourceRect.Top
            newW = sourceRect.Width
            newH = sourceRect.Height
 
            ' How much to change each dimension so the  animations all finish at the same time
            stepX = (newWindowRect.X - sourceRect.Left) / totalTicks
            stepY = (newWindowRect.Y - sourceRect.Top) / totalTicks
            stepW = (newWindowRect.Width - sourceRect.Width) / totalTicks
            stepH = (newWindowRect.Height - sourceRect.Height) / totalTicks
 
            ' Initialize the timers
            animateX.Interval = m\_Speed
            animateY.Interval = m\_Speed
            animateW.Interval = m\_Speed
            animateH.Interval = m\_Speed
            animateX.Enabled = True
            animateY.Enabled = True
            animateW.Enabled = True
            animateH.Enabled = True
 
            ' Animate X position change
            AddHandler animateX.Tick, Sub()
                                          ' Refresh object info
                                          GetWindowRect(SourceHandle, sourceRect)
 
                                          If sourceRect.Left <> newWindowRect.X Then
                                              ' Store the new dimension in a decimal-friendly veriable
                                              newX += stepX
                                              ' Change the dimension
                                              MoveWindow(SourceHandle, newX, sourceRect.Y, sourceRect.Width, sourceRect.Height, True)
                                          Else
                                              ' We're done here, disable the timer
                                              animateX.Enabled = False
                                          End If
                                      End Sub
 
            ' Animate Y position change
            AddHandler animateY.Tick, Sub()
                                          ' Refresh object info
                                          GetWindowRect(SourceHandle, sourceRect)
                                          If sourceRect.Top <> newWindowRect.Y Then
                                              ' Store the new dimension in a decimal-friendly veriable
                                              newY += stepY
                                              ' Change the dimension
                                              MoveWindow(SourceHandle, sourceRect.X, newY, sourceRect.Width, sourceRect.Height, True)
                                          Else
                                              ' We're done here, disable the timer
                                              animateY.Enabled = False
                                          End If
                                      End Sub
 
            ' Animate Width change
            AddHandler animateW.Tick, Sub()
                                          ' Refresh object info
                                          GetWindowRect(SourceHandle, sourceRect)
                                          If sourceRect.Width <> newWindowRect.Width Then
                                              ' Store the new dimension in a decimal-friendly veriable
                                              newW += stepW
                                              ' Change the dimension
                                              MoveWindow(SourceHandle, sourceRect.X, sourceRect.Y, newW, sourceRect.Height, True)
                                          Else
                                              ' We're done here, disable the timer
                                              animateW.Enabled = False
                                          End If
                                      End Sub
 
            ' Animate Height change
            AddHandler animateH.Tick, Sub()
                                          ' Refresh object info
                                          GetWindowRect(SourceHandle, sourceRect)
                                          If sourceRect.Height <> newWindowRect.Height Then
                                              ' Store the new dimension in a decimal-friendly veriable
                                              newH += stepH
                                              ' Change the dimension
                                              MoveWindow(SourceHandle, newX, newY, sourceRect.Width, newH, True)
                                          Else
                                              ' We're done here, disable the timer
                                              animateH.Enabled = False
                                          End If
                                      End Sub
        End Sub
 
    End Class
End Namespace
