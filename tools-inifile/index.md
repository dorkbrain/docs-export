---
title: "Tools.IniFile"
date: "2017-09-03"
categories: 
  - "vb-net"
tags: 
  - "vb"
---

Imports System.Text
Imports System.Runtime.InteropServices
 
Namespace Tools
  ''' <summary>
  ''' Wrapper class for accessing INI files.
  ''' </summary>
  Public Class IniFile
    <DllImport("kernel32.dll", SetLastError:=True)> \_
    Private Shared Function GetPrivateProfileInt(ByVal lpAddName As String,
                                          ByVal lpKeyName As String,
                                          ByVal nDefault As String,
                                          ByVal lpFileName As String) As Integer
    End Function
 
    <DllImport("kernel32.dll", SetLastError:=True)> \_
    Private Shared Function GetPrivateProfileString(ByVal lpAppName As String,
                            ByVal lpKeyName As String,
                            ByVal lpDefault As String,
                            ByVal lpReturnedString As StringBuilder,
                            ByVal nSize As Integer,
                            ByVal lpFileName As String) As Integer
    End Function
 
    <DllImport("kernel32.dll", SetLastError:=True)> \_
    Private Shared Function GetPrivateProfileSection(ByVal lpAppName As String,
                                                   ByVal lpReturnedString As Byte(),
                                                   ByVal nSize As Integer,
                                                   ByVal lpFileName As String) As Integer
    End Function
 
 
    <DllImport("kernel32.dll", SetLastError:=True)> \_
    Private Shared Function GetPrivateProfileSectionNames(ByVal lpszReturnBuffer As Byte(),
                                                          ByVal nSize As Integer,
                                                          ByVal lpFileName As String) As Integer
    End Function
 
    <DllImport("kernel32.dll", SetLastError:=True)> \_
    Private Shared Function WritePrivateProfileString(ByVal lpAppName As String,
                                                      ByVal lpKeyName As String,
                                                      ByVal lpString As String,
                                                      ByVal lpFileName As String) As Integer
    End Function
 
    <DllImport("kernel32.dll", SetLastError:=True)> \_
    Private Shared Function DeletePrivateProfileKey(ByVal lpAppName As String,
                                                    ByVal lpKeyName As String,
                                                    ByVal lpString As Integer,
                                                    ByVal lpFileName As String) As Integer
    End Function
 
    <DllImport("kernel32.dll", SetLastError:=True)> \_
    Private Shared Function DeletePrivateProfileSection(ByVal lpAppName As String,
                                                        ByVal lpKeyName As Integer,
                                                        ByVal lpString As Integer,
                                                        ByVal lpFileName As String) As Integer
    End Function
 
    <DllImport("kernel32.dll", SetLastError:=True)> \_
    Private Shared Function WritePrivateProfileSection(ByVal lpAppName As String,
                                                       ByVal lpString As Byte(),
                                                       ByVal lpFileName As String) As Integer
    End Function
 
 
    Private m\_filename As String = ""
 
    ''' <summary>
    ''' Creates a new IniFile instance.
    ''' </summary>
    Sub New()
    End Sub
 
    ''' <summary>
    ''' Creates a new IniFile instance.
    ''' </summary>
    ''' <param name="INIFileName">The full path and file name of the INI file.</param>
    Sub New(ByVal INIFileName As String)
      m\_filename = INIFileName
    End Sub
 
    ''' <summary>
    ''' Gets or sets the INI file name.
    ''' </summary>
    ''' <value>Gets or sets the INI file name.</value>
    Public Property FileName() As String
      Get
        Return m\_filename
      End Get
      Set(ByVal value As String)
        m\_filename = value
      End Set
    End Property
 
    ''' <summary>
    ''' Retreives a StringCollection of the entire contents of a section.
    ''' </summary>
    ''' <param name="SectionName">The name of the section you wish to get the contents of.</param>
    Public Function GetSection(ByVal SectionName As String) \_
              As Collections.Specialized.StringCollection
 
      If Not IsNothing(m\_filename) AndAlso Not m\_filename = "" Then
        Dim retval As New Collections.Specialized.StringCollection
        Dim buffer(32767) As Byte
        Dim bufLen As Integer = GetPrivateProfileSection(SectionName, buffer, buffer.GetUpperBound(0), m\_filename)
        If bufLen > 0 Then
          Dim sb As New System.Text.StringBuilder
          For idx As Integer = 0 To bufLen - 1
            If buffer(idx) <> 0 Then
              sb.Append(ChrW(buffer(idx)))
            Else
              retval.Add(sb.ToString)
              sb = New System.Text.StringBuilder
            End If
          Next
        End If
        Return retval
      Else
        Return Nothing
      End If
    End Function
 
    ''' <summary>
    ''' Retrieves a StringCollection of section names.
    ''' </summary>
    Public Function GetSectionNames() As Collections.Specialized.StringCollection
 
      If Not IsNothing(m\_filename) AndAlso Not m\_filename = "" Then
        Dim retval As New Collections.Specialized.StringCollection
        Dim buffer(32767) As Byte
        Dim bufLen As Integer = GetPrivateProfileSectionNames(buffer, buffer.GetUpperBound(0), m\_filename)
        If bufLen > 0 Then
          Dim sb As New System.Text.StringBuilder
          For idx As Integer = 0 To bufLen - 1
            If buffer(idx) <> 0 Then
              sb.Append(ChrW(buffer(idx)))
            Else
              retval.Add(sb.ToString)
              sb = New System.Text.StringBuilder
            End If
          Next
        End If
        Return retval
      Else
        Return Nothing
      End If
    End Function
 
    ''' <summary>
    ''' Overwrites or creates a section.
    ''' </summary>
    ''' <param name="SectionName">The name of the section.</param>
    ''' <param name="Contents">The contents you wish to assign the section.</param>
    Public Sub SetSection(ByVal SectionName As String, \_
           ByVal Contents As Collections.Specialized.StringCollection)
 
      If Not IsNothing(m\_filename) AndAlso Not m\_filename = "" Then
        DeleteSection(SectionName)
        Dim buffer(32767) As Byte
        Dim idx As Integer = 0
        For Each item As String In Contents
          System.Text.Encoding.ASCII.GetBytes(item, 0, item.Length, buffer, idx)
          idx += item.Length
          buffer(idx) = 0
          idx += 1
        Next
        WritePrivateProfileSection(SectionName, buffer, m\_filename)
      End If
    End Sub
 
    ''' <summary>
    ''' Adds to an already existing section.  If the section does not exist it will be created.
    ''' </summary>
    ''' <param name="SectionName">The name of the section.</param>
    ''' <param name="Contents">Tthe contents you wish to append to the section.</param>
    Public Sub AppendSection(ByVal SectionName As String, \_
           ByVal Contents As Collections.Specialized.StringCollection)
 
      If Not IsNothing(m\_filename) AndAlso Not m\_filename = "" Then
        Dim buffer(32767) As Byte
        Dim idx As Integer = 0
        For Each item As String In Contents
          System.Text.Encoding.ASCII.GetBytes(item, 0, item.Length, buffer, idx)
          idx += item.Length
          buffer(idx) = 0
          idx += 1
        Next
        WritePrivateProfileSection(SectionName, buffer, m\_filename)
      End If
    End Sub
 
    ''' <summary>
    ''' Returns the value of a key as an Integer.
    ''' </summary>
    ''' <param name="SectionName">The name of the section.</param>
    ''' <param name="KeyName">The name of the key to get the value from.</param>
    ''' <param name="Default">If the key was not found then return this default value.</param>
    Public Function GetInteger(ByVal SectionName As String, \_
             ByVal KeyName As String, \_
             Optional ByVal \[Default\] As Integer = -1) \_
             As Integer
 
      If Not IsNothing(m\_filename) AndAlso Not m\_filename = "" Then
        Return GetPrivateProfileInt(SectionName, KeyName, \[Default\], m\_filename)
      Else
        Return \[Default\]
      End If
    End Function
 
    ''' <summary>
    ''' Returns the value of a key as a String.
    ''' </summary>
    ''' <param name="SectionName">The name of the section.</param>
    ''' <param name="KeyName">The name of the key to get the value from.</param>
    ''' <param name="Default">If the key was not found then return this default value.</param>
    Public Function GetString(ByVal SectionName As String, \_
            ByVal KeyName As String, \_
            Optional ByVal \[Default\] As String = "") \_
            As String
 
      If Not IsNothing(m\_filename) AndAlso Not m\_filename = "" Then
        Dim buffer As New System.Text.StringBuilder(1024)
        Dim ret As Integer = GetPrivateProfileString(SectionName, KeyName, \[Default\], buffer, buffer.Capacity, m\_filename)
        If ret <> 0 Then
          Return buffer.ToString
        Else
          Return \[Default\]
        End If
      Else
        Return Nothing
      End If
    End Function
 
    ''' <summary>
    ''' Sets the value of a key as an Integer.
    ''' </summary>
    ''' <param name="SectionName">The name of the section.</param>
    ''' <param name="KeyName">The name of the key you wish to set the value for.</param>
    ''' <param name="Value">The value you wish to assign to the specified key.</param>
    Public Sub SetInteger(ByVal SectionName As String, \_
           ByVal KeyName As String, \_
           ByVal Value As Integer)
 
      If Not IsNothing(m\_filename) AndAlso Not m\_filename = "" Then
        WritePrivateProfileString(SectionName, KeyName, Value.ToString, m\_filename)
      End If
    End Sub
 
    ''' <summary>
    ''' Sets the value of a key as a String.
    ''' </summary>
    ''' <param name="SectionName">The name of the section.</param>
    ''' <param name="KeyName">The name of the key you wish to set the value for.</param>
    ''' <param name="Value">The value you wish to assign to the specified key.</param>
    Public Sub SetString(ByVal SectionName As String, \_
          ByVal KeyName As String, \_
          ByVal Value As String)
 
      If Not IsNothing(m\_filename) AndAlso Not m\_filename = "" Then
        WritePrivateProfileString(SectionName, KeyName, Value, m\_filename)
      End If
    End Sub
 
    ''' <summary>
    ''' Deletes a key from within a section.
    ''' </summary>
    ''' <param name="SectionName">The name of the section.</param>
    ''' <param name="KeyName">The name of the key you wish to delete.</param>
    Public Sub DeleteKey(ByVal SectionName As String, \_
          ByVal KeyName As String)
 
      If Not IsNothing(m\_filename) AndAlso Not m\_filename = "" Then
        DeletePrivateProfileKey(SectionName, KeyName, 0, m\_filename)
      End If
    End Sub
 
    ''' <summary>
    ''' Deletes an entire section.
    ''' </summary>
    ''' <param name="SectionName">The name of the section you wish to delete.</param>
    Public Sub DeleteSection(ByVal SectionName As String)
 
      If Not IsNothing(m\_filename) AndAlso Not m\_filename = "" Then
        DeletePrivateProfileSection(SectionName, 0, 0, m\_filename)
      End If
    End Sub
  End Class
End Namespace
