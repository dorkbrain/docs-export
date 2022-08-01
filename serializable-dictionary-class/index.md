---
title: "Serializable Dictionary Class"
date: "2017-09-02"
categories: 
  - "vb-net"
tags: 
  - "vb"
---

Imports System.IO
Imports System.Runtime.Serialization.Formatters.Binary
 
Public Class SerializableDictionary(Of TKey, TValue)
    Inherits System.Collections.Generic.Dictionary(Of TKey, TValue)
    Public FileName As String = "MySettings.bin"
 
    Public Sub Save()
        Using fs As New FileStream(FileName, FileMode.Create)
            Dim formatter As New BinaryFormatter
            Dim tempdict As New Dictionary(Of TKey, TValue)
            For Each obj As KeyValuePair(Of TKey, TValue) In Me
                tempdict.Add(obj.Key, obj.Value)
            Next
            formatter.Serialize(fs, tempdict)
        End Using
    End Sub
 
    Public Sub Save(ByVal DictionaryFile As String)
        FileName = DictionaryFile
        Save()
    End Sub
 
    Public Sub Load()
        If My.Computer.FileSystem.FileExists(FileName) Then
            Using fs As New FileStream(FileName, FileMode.OpenOrCreate)
                Dim formatter As New BinaryFormatter
                Dim tempdict As Dictionary(Of TKey, TValue) = DirectCast(formatter.Deserialize(fs),  \_
                                                                         Dictionary(Of TKey, TValue))
                Me.Clear()
                For Each obj As KeyValuePair(Of TKey, TValue) In tempdict
                    Me.Add(obj.Key, obj.Value)
                Next
            End Using
        Else
            Me.Clear()
        End If
    End Sub
 
    Public Sub Load(ByVal DictionaryFile As String)
        FileName = DictionaryFile
        Load()
    End Sub
End Class
