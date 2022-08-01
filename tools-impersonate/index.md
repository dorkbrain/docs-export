---
title: "Tools.Impersonate"
date: "2017-09-03"
categories: 
  - "vb-net"
tags: 
  - "vb"
---

Imports System.Security.Principal
 
Namespace Tools
    Public Module Impersonate
        Public Enum LogonType As Integer
            'This logon type is intended for users who will be interactively using the computer, such as a user being logged on 
            'by a terminal server, remote shell, or similar process.
            'This logon type has the additional expense of caching logon information for disconnected operations; 
            'therefore, it is inappropriate for some client/server applications,
            'such as a mail server.
            LOGON32\_LOGON\_INTERACTIVE = 2
 
            'This logon type is intended for high performance servers to authenticate plaintext passwords.
            'The LogonUser function does not cache credentials for this logon type.
            LOGON32\_LOGON\_NETWORK = 3
 
            'This logon type is intended for batch servers, where processes may be executing on behalf of a user without 
            'their direct intervention. This type is also for higher performance servers that process many plaintext
            'authentication attempts at a time, such as mail or Web servers. 
            'The LogonUser function does not cache credentials for this logon type.
            LOGON32\_LOGON\_BATCH = 4
 
            'Indicates a service-type logon. The account provided must have the service privilege enabled. 
            LOGON32\_LOGON\_SERVICE = 5
 
            'This logon type is for GINA DLLs that log on users who will be interactively using the computer. 
            'This logon type can generate a unique audit record that shows when the workstation was unlocked. 
            LOGON32\_LOGON\_UNLOCK = 7
 
            'This logon type preserves the name and password in the authentication package, which allows the server to make 
            'connections to other network servers while impersonating the client. A server can accept plaintext credentials 
            'from a client, call LogonUser, verify that the user can access the system across the network, and still 
            'communicate with other servers.
            'NOTE: Windows NT:  This value is not supported. 
            LOGON32\_LOGON\_NETWORK\_CLEARTEXT = 8
 
            'This logon type allows the caller to clone its current token and specify new credentials for outbound connections.
            'The new logon session has the same local identifier but uses different credentials for other network connections.
            'NOTE: This logon type is supported only by the LOGON32\_PROVIDER\_WINNT50 logon provider.
            'NOTE: Windows NT:  This value is not supported. 
            LOGON32\_LOGON\_NEW\_CREDENTIALS = 9
        End Enum
 
        Public Enum LogonProvider As Integer
            'Use the standard logon provider for the system. 
            'The default security provider is negotiate, unless you pass NULL for the domain name and the user name 
            'is not in UPN format. In this case, the default provider is NTLM. 
            'NOTE: Windows 2000/NT:   The default security provider is NTLM.
            LOGON32\_PROVIDER\_DEFAULT = 0
            LOGON32\_PROVIDER\_WINNT35 = 1
            LOGON32\_PROVIDER\_WINNT40 = 2
            LOGON32\_PROVIDER\_WINNT50 = 3
        End Enum
 
        Private Declare Auto Function LogonUser Lib "advapi32.dll" (ByVal lpszUsername As String, \_
            ByVal lpszDomain As String, ByVal lpszPassword As String, ByVal dwLogonType As LogonType, \_
            ByVal dwLogonProvider As LogonProvider, ByRef phToken As IntPtr) As Boolean
 
        Private Declare Auto Function CloseHandle Lib "kernel32.dll" (hHandle As IntPtr) As Boolean
 
        Private userHandle As IntPtr = IntPtr.Zero
        Private impersonationContext As WindowsImpersonationContext = Nothing
 
        Public Function StartImpersonation(userName As String, userDomain As String, password As String) As Boolean
            Dim loggedOn As Boolean = LogonUser(userName, userDomain, password, LogonType.LOGON32\_LOGON\_INTERACTIVE, LogonProvider.LOGON32\_PROVIDER\_DEFAULT, userHandle)
            If loggedOn Then
                impersonationContext = WindowsIdentity.Impersonate(userHandle)
            End If
            Return loggedOn
        End Function
 
        Public Sub StopImpersonation()
            CloseHandle(userHandle)
            impersonationContext.Undo()
        End Sub
    End Module
End Namespace
