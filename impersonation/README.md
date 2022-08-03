---
title: "Impersonation"
date: "2017-09-02"
categories: 
  - "vb-net"
tags: 
  - "vb"
---

Imports System.Security.Principal
Imports System.Runtime.InteropServices

Public Class Impersonation

    'These constants are based on values in the Win32 API file WINBASE.H
    'Refer to http://msdn.microsoft.com/en-us/library/aa378184(VS.85).aspx for their meanings
    Const LOGON32\_LOGON\_INTERACTIVE As Integer = 2
    Const LOGON32\_PROVIDER\_DEFAULT As Integer = 0

    Private Declare Function LogonUserA Lib "advapi32.dll" (ByVal lpszUsername As String, \_
                            ByVal lpszDomain As String, \_
                            ByVal lpszPassword As String, \_
                            ByVal dwLogonType As Integer, \_
                            ByVal dwLogonProvider As Integer, \_
                            ByRef phToken As IntPtr) As Integer

    Private Declare Auto Function DuplicateToken Lib "advapi32.dll" ( \_
                            ByVal ExistingTokenHandle As IntPtr, \_
                            ByVal ImpersonationLevel As SECURITY\_IMPERSONATION\_LEVEL, \_
                            ByRef DuplicateTokenHandle As IntPtr) As Integer

    Private Declare Auto Function CloseHandle Lib "kernel32.dll" (ByVal handle As IntPtr) As Long

    Private Enum SECURITY\_IMPERSONATION\_LEVEL As Integer
        SecurityAnonymous = 0
        SecurityIdentification = 1
        SecurityImpersonation = 2
        SecurityDelegation = 3
    End Enum

    Private m\_oImpersonationContext As WindowsImpersonationContext
    Private m\_bImpersonationActive As Boolean

    Public Sub New()

    End Sub

    Public ReadOnly Property ImpersonationActive() As Boolean
        Get
            Return m\_bImpersonationActive
        End Get
    End Property

    Public Function StartImpersonation(ByVal sUserName As String, ByVal sDomain As String, ByVal sPassword As String) As Boolean
        Dim bResults As Boolean = False
        Dim sErrorMessage As String
        Dim oWindowsIdentity As WindowsIdentity
        Dim hPrimaryToken As IntPtr = IntPtr.Zero           'a Win32 handle to our authentication token
        Dim hImpersonationToken As IntPtr = IntPtr.Zero     'a Win32 handle to our impersonation token

        If String.IsNullOrEmpty(sUserName) Then
            Throw New ArgumentException("UserName may not be NULL or String.Empty")
        End If

        'If no domain is given, assume the account is a local one
        If sDomain = String.Empty Then
            sDomain = Environment.MachineName
        End If

        Try
            'Validate the provided userid, password and domain.
            If LogonUserA(sUserName, sDomain, sPassword, LOGON32\_LOGON\_INTERACTIVE, LOGON32\_PROVIDER\_DEFAULT, hPrimaryToken) <> 0 Then

                'Convert our token to one whos handle has TOKEN\_IMPERSONATE set
                If DuplicateToken(hPrimaryToken, SECURITY\_IMPERSONATION\_LEVEL.SecurityImpersonation, hImpersonationToken) <> 0 Then

                    'Create a new identity object based on our impersonation token
                    oWindowsIdentity = New WindowsIdentity(hImpersonationToken)

                    'Switch to our new identity
                    m\_oImpersonationContext = oWindowsIdentity.Impersonate()
                    If m\_oImpersonationContext IsNot Nothing Then
                        m\_bImpersonationActive = True
                        bResults = True
                    End If
                Else
                    sErrorMessage = String.Format("DuplicateToken failed (rc={0})", Runtime.InteropServices.Marshal.GetLastWin32Error)
                    Throw New Security.Authentication.AuthenticationException(sErrorMessage)
                End If
            Else
                sErrorMessage = String.Format("LogonUser failed (rc={0})", Runtime.InteropServices.Marshal.GetLastWin32Error)
                Throw New Security.Authentication.AuthenticationException(sErrorMessage)
            End If

        Finally
            If Not hImpersonationToken.Equals(IntPtr.Zero) Then
                CloseHandle(hImpersonationToken)
                hImpersonationToken = IntPtr.Zero
            End If
            If Not hPrimaryToken.Equals(IntPtr.Zero) Then
                CloseHandle(hPrimaryToken)
                hPrimaryToken = IntPtr.Zero
            End If
        End Try

        Return bResults
    End Function

    Public Sub EndImpersonation()
        If m\_oImpersonationContext IsNot Nothing AndAlso m\_bImpersonationActive Then
            m\_oImpersonationContext.Undo()
            m\_oImpersonationContext.Dispose()
            m\_oImpersonationContext = Nothing
            m\_bImpersonationActive = False
        End If
    End Sub
End Class
