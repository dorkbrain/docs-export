---
title: "Tools.CommandLineProcessor"
date: "2017-09-03"
categories: 
  - "vb-net"
tags: 
  - "vb"
---

Namespace Tools
  Public Class CommandLineProcessor
    Private m\_Args As New Collection
    Private m\_Flags As New Collection
    Private m\_Params As New Dictionary(Of String, String)
    Private m\_Extras As New Collection
    Private Const vbQuote As Char = Chr(34)
    Private Const vbSpace As Char = " "c
 
    Private Sub PopulateArgs()
      Dim commandLine As String = System.Environment.CommandLine
      ' Replace any escaped double-quotes (\\") with a placeholder
      commandLine = commandLine.Replace("\\" & vbQuote, Chr(255))
 
      Dim inQuotes As Boolean = False
      Dim thisArg As String = ""
 
      For Each thisChar As Char In commandLine.ToCharArray
        If thisChar = vbQuote Then
          ' is this a beginning or ending quote?
          inQuotes = Not inQuotes
        Else
          If Not inQuotes And thisChar = vbSpace Then
            ' We're not inside quotes, this is the end of the argument
            ' Put the escaped double-quotes back, minus the \\
            thisArg = thisArg.Replace(Chr(255), vbQuote)
            If Not thisArg = "" Then m\_Args.Add(thisArg)
            thisArg = ""
          Else
            ' If we're inside quotes we shouldn't split when we see a space
            thisArg &= thisChar.ToString
          End If
        End If
      Next
 
      ' Put the escaped double-quotes back, minus the \\
      thisArg = thisArg.Replace(Chr(255), vbQuote)
      If Not thisArg = "" Then m\_Args.Add(thisArg)
    End Sub
 
    Private Sub ParseArgs(ByVal splitParamChars As Char(), Optional ByVal argPrefix As String = "/")
      Dim argCount As Integer = -1
      For Each arg As String In m\_Args
        argCount += 1
        ' Arg #0 is the full path to this program so ignore it.
        If argCount > 0 Then
          If arg.StartsWith(argPrefix) Then
            If arg.Contains(splitParamChars) Then
              'this is a param
              Dim argParts As String() = arg.Split(splitParamChars, 2)
              m\_Params.Add(argParts(0), argParts(1))
            Else
              'this is a flag
              m\_Flags.Add(arg)
            End If
          Else
            'this is extra text
            m\_Extras.Add(arg)
          End If
        End If
      Next
    End Sub
 
    Public Sub New()
      Me.New(":")
    End Sub
 
    Public Sub New(ByVal splitParamChars As String, Optional ByVal argPrefix As String = "/")
      Me.New(splitParamChars.ToCharArray, argPrefix)
    End Sub
 
    Public Sub New(ByVal splitParamChars As Char(), Optional ByVal argPrefix As String = "/")
      PopulateArgs()
      ParseArgs(splitParamChars, argPrefix)
    End Sub
 
    Public ReadOnly Property IsFlagPresent(ByVal flagText As String, Optional ByVal caseSensitive As Boolean = False)
      Get
        If caseSensitive Then flagText = flagText.ToLower
        For Each thisItem As String In m\_Flags
          If caseSensitive Then thisItem = thisItem.ToLower
          If thisItem = flagText Then Return True
        Next
        Return False
      End Get
    End Property
 
    Public ReadOnly Property IsParamPresent(ByVal paramText As String, Optional ByVal caseSensitive As Boolean = False) As Boolean
      Get
        If caseSensitive Then paramText = paramText.ToLower
        For Each thisItem As KeyValuePair(Of String, String) In m\_Params
          Dim compareToThis As String = thisItem.Key
          If caseSensitive Then compareToThis = compareToThis.ToLower
          If compareToThis = paramText Then Return True
        Next
        Return False
      End Get
    End Property
 
    Public ReadOnly Property ParamValue(ByVal paramText As String, Optional ByVal caseSensitive As Boolean = False) As String
      Get
        If caseSensitive Then paramText = paramText.ToLower
        For Each thisItem As KeyValuePair(Of String, String) In m\_Params
          Dim compareToThis As String = thisItem.Key
          If caseSensitive Then compareToThis = compareToThis.ToLower
          If compareToThis = paramText Then Return thisItem.Value
        Next
        Return ""
      End Get
    End Property
 
    Public ReadOnly Property ExtrasPresent As Boolean
      Get
        If m\_Extras.Count > 0 Then
          Return True
        Else
          Return False
        End If
      End Get
    End Property
 
    Public ReadOnly Property Params As Dictionary(Of String, String)
      Get
        Return m\_Params
      End Get
    End Property
 
    Public ReadOnly Property Extras As Collection
      Get
        Return m\_Extras
      End Get
    End Property
 
    Public ReadOnly Property Flags As Collection
      Get
        Return m\_Flags
      End Get
    End Property
 
    Public ReadOnly Property FullArgList As Collection
      Get
        Return m\_Args
      End Get
    End Property
 
    Public ReadOnly Property CommandString As String
      Get
        Return System.Environment.CommandLine
      End Get
    End Property
 
    Public ReadOnly Property ArgCount As Integer
      Get
        Return m\_Args.Count
      End Get
    End Property
  End Class
End Namespace
