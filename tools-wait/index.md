---
title: "Tools.Wait"
date: "2017-09-03"
categories: 
  - "vb-net"
tags: 
  - "vb"
---

Imports System.Threading.Tasks
 
Namespace Tools
  Public Class Wait
    Public Shared Sub Start(milliseconds As Integer)
      Task.WaitAll(Task.Factory.StartNew(Sub() Threading.Thread.Sleep(milliseconds)))
    End Sub
 
    Public Shared Sub OneSec()
      Tools.Wait.Start(1000)
    End Sub
  End Class
End Namespace
