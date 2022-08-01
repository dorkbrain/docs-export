---
title: "Tools.ConsoleWriteEx"
date: "2017-09-03"
categories: 
  - "vb-net"
tags: 
  - "vb"
---

Namespace Tools
  Public Class ConsoleWriteEx
    ' Adds C style escape sequences (\\n, \\t, etc) to Write and Writeline
    
    Shared Sub Write(ByVal Text As String)
        Write(Text, New Object())
    End Sub
 
    Shared Sub Write(ByVal Text As String, ByVal ParamArray arg() As Object)
        Text = String.Format(Text, arg)
        Console.Write(System.Text.RegularExpressions.Regex.Unescape(Text))
    End Sub
 
    Shared Sub WriteLine(ByVal Text As String)
        Write(Text & "\\n")
    End Sub
 
    Shared Sub WriteLine(ByVal Text As String, ByVal ParamArray arg() As Object)
        Write(Text & "\\n", arg)
    End Sub
 
  End Class
End Namespace
