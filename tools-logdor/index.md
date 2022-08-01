---
title: "Tools.Logdor"
date: "2017-09-03"
categories: 
  - "vb-net"
tags: 
  - "vb"
---

Imports System.Reflection
 
Namespace Tools
    Public Class Logdor
        Implements IDisposable
 
        Private m\_LogToConsole As Boolean = False
        Public Property LogToConsole As Boolean
            Get
                Return m\_LogToConsole
            End Get
            Set(ByVal value As Boolean)
                m\_LogToConsole = value
            End Set
        End Property
 
        Private m\_LogToEventLog As Boolean = False
        Public Property LogToEventLog As Boolean
            Get
                Return m\_LogToEventLog
            End Get
            Set(ByVal value As Boolean)
                m\_LogToEventLog = value
            End Set
        End Property
 
        Private m\_EventLogSource As String = ".NET Application"
        Public Property EventLogSource As String
            Get
                Return m\_EventLogSource
            End Get
            Set(ByVal value As String)
                m\_EventLogSource = value
            End Set
        End Property
 
        Private m\_LogToFile As Boolean = False
        Public Property LogToFile As Boolean
            Get
                Return m\_LogToFile
            End Get
            Set(ByVal value As Boolean)
                m\_LogToFile = value
            End Set
        End Property
 
        Private m\_LogFile As String = "log.txt"
        Public Property LogFile As String
            Get
                Return m\_LogFile
            End Get
            Set(ByVal value As String)
                m\_LogFile = value
            End Set
        End Property
 
        Private m\_UseTimestamp As Boolean = True
        Public Property UseTimeStamp As Boolean
            Get
                Return m\_UseTimestamp
            End Get
            Set(ByVal value As Boolean)
                m\_UseTimestamp = value
            End Set
        End Property
 
        Private m\_TimestampFormat As String = "MM-dd-yy @ HH.mm.ss"
        Public Property TimestampFormat As String
            Get
                Return m\_TimestampFormat
            End Get
            Set(ByVal value As String)
                m\_TimestampFormat = value
            End Set
        End Property
        Public ReadOnly Property Timestamp As String
            Get
                Return String.Format(m\_TimestampFormat, Now)
            End Get
        End Property
 
        Private m\_IndentLevel As Integer = 0
        Public Property IndentLevel As Integer
            Get
                Return m\_IndentLevel
            End Get
            Set(ByVal value As Integer)
                m\_IndentLevel = value
                If m\_IndentLevel < 0 Then m\_IndentLevel = 0
            End Set
        End Property
 
        Private m\_IndentChars As String = "  "
        Public Property IntentChars As String
            Get
                Return m\_IndentChars
            End Get
            Set(ByVal value As String)
                m\_IndentChars = value
            End Set
        End Property
 
        Private Function IndentString() As String
            Return StrDup(m\_IndentLevel, m\_IndentChars)
        End Function
 
        Public Enum Indent As Integer
            Add
            Subtract
            NoChange
        End Enum
 
        Public Function IndentAdd() As Integer
            IndentLevel += 1
            Return m\_IndentLevel
        End Function
 
        Public Function IndentSubtract() As Integer
            IndentLevel -= 1
            Return m\_IndentLevel
        End Function
 
        Public Function ObjectPropertyList(ByRef obj As Object, ByVal objname As String) As List(Of String)
            Dim retval As New List(Of String)
            For Each prop As PropertyInfo In obj.GetType.GetProperties
                Dim propline As New Text.StringBuilder
                propline.Append("\[Property\]:")
                If objname <> "" Then propline.Append(objname & ".")
                propline.Append(prop.Name & " = ")
                Try
                    If prop.GetValue(obj, New Object() {}) Is Nothing Then
                        propline.AppendLine("<nothing>")
                    Else
                        propline.AppendLine(prop.GetValue(obj, New Object() {}))
                    End If
                Catch ex As Exception
                    propline.AppendLine("Error")
                End Try
                retval.Add(propline.ToString)
            Next
            Return retval
        End Function
 
        Public Function ObjectPropertyString(ByRef obj As Object, ByVal objname As String) As String
            Dim retval As New Text.StringBuilder
            For Each item As String In ObjectPropertyList(obj, objname)
                retval.AppendLine(IndentString() & item)
            Next
            Return retval.ToString
        End Function
 
        Private LogFileIsOpen As Boolean = False
        Private LogWriter As IO.StreamWriter = Nothing
        Public Sub OpenLogFile()
            If Not LogFileIsOpen Then
                Try
                    LogWriter = New IO.StreamWriter(LogFile, True)
                    LogFileIsOpen = True
                Catch ex As Exception
                    LogFileIsOpen = False
                End Try
            End If
        End Sub
        Public Sub CloseLogFile()
            If LogFileIsOpen Then
                Try
                    LogWriter.Flush()
                    LogWriter.Close()
                    LogWriter = Nothing
                    LogFileIsOpen = False
                Catch ex As Exception
                End Try
            End If
        End Sub
 
        Public Sub WriteLog(ByVal LogText As String)
            WriteLog(LogText, EventLogEntryType.Information, 0, Indent.NoChange)
        End Sub
 
        Public Sub WriteLog(ByVal LogText As String, ByVal IndentChange As Indent)
            WriteLog(LogText, EventLogEntryType.Information, 0, IndentChange)
        End Sub
 
        Public Sub WriteLog(ByVal LogText As String, ByVal EventLogType As EventLogEntryType)
            WriteLog(LogText, EventLogType, 0, Indent.NoChange)
        End Sub
 
        Public Sub WriteLog(ByVal LogText As String, ByVal EventLogType As EventLogEntryType, ByVal EventID As Integer)
            WriteLog(LogText, EventLogType, EventID, Indent.NoChange)
        End Sub
 
        Public Sub WriteLog(ByVal LogText As String, ByVal EventLogType As EventLogEntryType, ByVal EventID As Integer, ByVal IndentChange As Indent)
            Dim LogLine As New Text.StringBuilder
 
            If IndentChange = Indent.Add Then IndentLevel += 1
            If IndentChange = Indent.Subtract Then IndentLevel -= 1
 
            If UseTimeStamp Then
                LogLine.Append(Timestamp)
            End If
            LogLine.Append(IndentString)
            LogLine.Append(LogText)
 
 
            Debug.WriteLine(LogLine.ToString)
 
            If LogToConsole Then
                Try
                    Console.WriteLine(LogLine.ToString)
                Catch ex As Exception
                    LogToConsole = False
                    WriteLog("Error writing to console: " & ex.Message)
                End Try
 
            End If
 
 
            If LogToFile Then
                Try
                    If Not LogFileIsOpen Then OpenLogFile()
                    LogWriter.WriteLine(LogLine.ToString)
                Catch ex As Exception
                    LogToFile = False
                    WriteLog("Error writing to file: " & ex.Message)
                End Try
            End If
 
 
            If LogToEventLog Then
                Try
                    EventLog.WriteEntry(EventLogSource, LogText, EventLogType, EventID)
                Catch ex As Exception
                    LogToEventLog = False
                    WriteLog("Error writing to event log: " & ex.Message)
                End Try
            End If
        End Sub
 
        Private disposedValue As Boolean
        Protected Overridable Sub Dispose(ByVal disposing As Boolean)
            If Not Me.disposedValue Then
                If disposing Then
                    If LogFileIsOpen Then
                        CloseLogFile()
                    End If
                End If
            End If
            Me.disposedValue = True
        End Sub
        Public Sub Dispose() Implements IDisposable.Dispose
            Dispose(True)
            GC.SuppressFinalize(Me)
        End Sub
    End Class
End Namespace
