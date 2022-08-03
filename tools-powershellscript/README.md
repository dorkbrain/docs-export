---
title: "Tools.PowerShellScript"
date: "2017-09-03"
categories: 
  - "vb-net"
tags: 
  - "vb"
---

' Add a reference to:
' C:\\Program Files\\Reference Assemblies\\Microsoft\\WindowsPowerShell\\v1.0\\System.Management.Automation.dll
Imports System.Collections.ObjectModel
Imports System.Management.Automation
Imports System.Management.Automation.Runspaces
 
Imports System.Text
Imports System.IO
 
Namespace Tools
    Public Class PowerShellScript
        Public Event StatusUpdate(ByVal status As String)
 
        Public Function RunScriptText(ByVal scriptText As String) As String
            'Runs PS code and returns the response in string format

            'Create a PS runspace and open it
            RaiseEvent StatusUpdate("Creating runspace")
            Dim myRunSpace As Runspace = RunspaceFactory.CreateRunspace
            myRunSpace.Open()
 
            'Create a pipeline and load it with our script text
            RaiseEvent StatusUpdate("Creating pipeline")
            Dim myPipeline As Pipeline = myRunSpace.CreatePipeline
            myPipeline.Commands.AddScript(scriptText)
 
            'add an extra command to ensure we get a string output
            myPipeline.Commands.Add("Out-String")
 
            'Execute
            RaiseEvent StatusUpdate("Executing script")
            Dim results As Collection(Of PSObject) = myPipeline.Invoke
 
            'Close the runspace
            RaiseEvent StatusUpdate("Closing runspace")
            myRunSpace.Close()
 
            'Put the results in a single string
            RaiseEvent StatusUpdate("Returning results")
            Dim myStringBuilder As New StringBuilder
 
            For Each obj As PSObject In results
                myStringBuilder.AppendLine(obj.ToString)
            Next
 
            'return the results
            Return myStringBuilder.ToString
        End Function
 
        Public Function LoadScriptFile(ByVal scriptFile As String) As String
            Try
                RaiseEvent StatusUpdate("Opening script file")
                Dim sr As New StreamReader(scriptFile)
                RaiseEvent StatusUpdate("Reading script contents")
                Return sr.ReadToEnd()
            Catch ex As Exception
                RaiseEvent StatusUpdate("Error reading file")
                Return "Write-Host 'The file could not be read.'"
            End Try
        End Function
    End Class
End Namespace
