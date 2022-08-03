---
title: "Tools.ServiceWrapper"
date: "2017-09-03"
categories: 
  - "vb-net"
tags: 
  - "vb"
---

Imports System.Runtime.InteropServices
 
Namespace Tools
	Public Class ServiceWrapper
		<DllImport("advapi32.dll", EntryPoint:="OpenSCManagerW", SetLastError:=True, CharSet:=CharSet.Unicode)> \_
		Private Shared Function OpenSCManager(ByVal lpMachingName As String, \_
									  ByVal lpDatabaseName As String, \_
									  ByVal dwDesiredAccess As SCM\_ACCESS) As IntPtr
		End Function
 
		<DllImport("advapi32.dll", EntryPoint:="OpenServiceW", SetLastError:=True, CharSet:=CharSet.Unicode)> \_
		Private Shared Function OpenService(ByVal SC\_HANDLE As IntPtr, \_
									ByVal strServiceName As String, \_
									ByVal dwDesiredAccess As SERVICE\_ACCESS) As IntPtr
		End Function
 
		<DllImport("advapi32.dll", EntryPoint:="CloseServiceHandle", SetLastError:=True, CharSet:=CharSet.Unicode)> \_
		Private Shared Function CloseServiceHandle(ByVal SC\_HANDLE As IntPtr) As Boolean
		End Function
 
		<DllImport("advapi32.dll", EntryPoint:="QueryServiceConfigW", SetLastError:=True, CharSet:=CharSet.Unicode)> \_
		Private Shared Function QueryServiceConfig(ByVal SC\_HANDLE As IntPtr, \_
										   ByVal pBuffer As IntPtr, \_
										   ByVal cbBufSize As Int32, \_
										   ByRef pcbBytesNeeded As Int32) As Boolean
		End Function
 
		<DllImport("advapi32.dll", EntryPoint:="QueryServiceConfig2W", SetLastError:=True, CharSet:=CharSet.Unicode)> \_
		Private Shared Function QueryServiceConfig2(ByVal SC\_HANDLE As IntPtr, \_
											ByVal dwInfoLevel As Int32, \_
											ByVal pBuffer As IntPtr, \_
											ByVal cbBufSize As Int32, \_
											ByRef pcbBytesNeeded As Int32) As Boolean
		End Function
 
		<DllImport("advapi32.dll", EntryPoint:="CreateServiceW", SetLastError:=True, CharSet:=CharSet.Unicode)> \_
		Private Shared Function CreateService(ByVal SC\_HANDLE As IntPtr, \_
									  ByVal lpServiceName As String, \_
									  ByVal lpDisplayName As String, \_
									  ByVal dwDesiredAccess As SERVICE\_ACCESS, \_
									  ByVal dwServiceType As SERVICE\_TYPE, \_
									  ByVal dwStartType As SERVICE\_START\_TYPE, \_
									  ByVal dwErrorControl As SERVICE\_ERROR\_CONTROL, \_
									  ByVal lpBinaryPathName As String, \_
									  ByVal lpLoadOrderGroup As String, \_
									  ByVal lpdwTagId As Int32, \_
									  ByVal lpDependencies As String, \_
									  ByVal lpServiceUserName As String, \_
									  ByVal lpPassword As String) As IntPtr
		End Function
 
		<DllImport("advapi32.dll", EntryPoint:="DeleteService", SetLastError:=True, CharSet:=CharSet.Unicode)> \_
		Private Shared Function DeleteService(ByVal SC\_HANDLE As IntPtr) As Boolean
		End Function
 
		<DllImport("advapi32.dll", entryPoint:="ChangeServiceConfigW", SetLastError:=True, CharSet:=CharSet.Unicode)> \_
		Private Shared Function ChangeServiceConfig(ByVal SC\_HANDLE As IntPtr, \_
									 ByVal dwServiceType As SERVICE\_TYPE, \_
									 ByVal dwStartType As SERVICE\_START\_TYPE, \_
									 ByVal dwErrorControl As SERVICE\_ERROR\_CONTROL, \_
									 ByVal lpBinaryPathName As String, \_
									 ByVal lpLoadOrderGroup As String, \_
									 ByVal lpdwTagId As Int32, \_
									 ByVal lpDependencies As String, \_
									 ByVal lpServiceStartName As String, \_
									 ByVal lpPassword As String, \_
									 ByVal lpDisplayName As String) As Boolean
		End Function
 
		<DllImport("advapi32.dll", entryPoint:="ChangeServiceConfig2W", SetLastError:=True, CharSet:=CharSet.Unicode)> \_
		Private Shared Function ChangeServiceConfig2(ByVal SC\_HANDLE As IntPtr, \_
									  ByVal dwInfoLevel As Int32, \_
									  ByVal lpInfo As IntPtr) As Boolean
		End Function
 
		Private Enum PVT\_WIN32\_ERRORS As Int32
			ERROR\_SUCCESS = &H0
			ERROR\_ACCESS\_DENIED = &H5
			ERROR\_INVALID\_HANDLE = &H6
			ERROR\_INVALID\_PARAMETER = &H57
			ERROR\_INSUFFICIENT\_BUFFER = &H7A
			ERROR\_INVALID\_NAME = &H7B
			ERROR\_INVALID\_SERVICE\_ACCOUNT = &H421
			ERROR\_CIRCULAR\_DEPENDENCY = &H423
			ERROR\_SERVICE\_DOES\_NOT\_EXIST = &H424
			ERROR\_DATABASE\_DOES\_NOT\_EXIST = &H429
			ERROR\_SERVICE\_EXISTS = &H431
			ERROR\_DUPLICATE\_SERVICE\_NAME = &H436
			ERROR\_SERVICE\_MARKED\_FOR\_DELETE = &H430
		End Enum
 
		Private Enum SCM\_ACCESS As Int32
			SC\_MANAGER\_CONNECT = &H1
			SC\_MANAGER\_CREATE\_SERVICE = &H2
			SC\_MANAGER\_ENUMERATE\_SERVICE = &H4
			SC\_MANAGER\_LOCK = &H8
			SC\_MANAGER\_QUERY\_LOCK\_STATUS = &H10
			SC\_MANAGER\_MODIFY\_BOOT\_CONFIG = &H20
			STANDARD\_RIGHTS\_REQUIRED = &HF000
			GENERIC\_WRITE = &H40000000
			SC\_MANAGER\_ALL\_ACCESS = STANDARD\_RIGHTS\_REQUIRED And SC\_MANAGER\_CONNECT And SC\_MANAGER\_CREATE\_SERVICE And SC\_MANAGER\_ENUMERATE\_SERVICE And SC\_MANAGER\_LOCK And SC\_MANAGER\_QUERY\_LOCK\_STATUS And SC\_MANAGER\_MODIFY\_BOOT\_CONFIG
		End Enum
 
		Private Enum SERVICE\_ACCESS As Int32
			SERVICE\_QUERY\_CONFIG = &H1
			SERVICE\_CHANGE\_CONFIG = &H2
			SERVICE\_QUERY\_STATUS = &H4
			SERVICE\_ENUMERATE\_DEPENDENTS = &H8
			SERVICE\_START = &H10
			SERVICE\_STOP = &H20
			SERVICE\_PAUSE\_CONTINUE = &H40
			SERVICE\_INTERROGATE = &H80
			SERVICE\_USER\_DEFINED\_CONTROL = &H100
			SERVICE\_DELETE = &H10000
			SERVICE\_ALL\_RIGHTS = &HF01FF
		End Enum
 
		Public Enum SERVICE\_TYPE As Int32
			SERVICE\_KERNEL\_DRIVER = &H1
			SERVICE\_FILE\_SYSTEM\_DRIVER = &H2
			SERVICE\_WIN32\_OWN\_PROCESS = &H10
			SERVICE\_WIN32\_SHARE\_PROCESS = &H20
			SERVICE\_INTERACTIVE\_PROCESS = &H100
			SERVICE\_NO\_CHANGE = &HFFFFFFFF
		End Enum
 
		Public Enum SERVICE\_START\_TYPE As Int32
			SERVICE\_BOOT\_START = &H0
			SERVICE\_SYSTEM\_START = &H1
			SERVICE\_AUTO\_START = &H2
			SERVICE\_DEMAND\_START = &H3
			SERVICE\_DISABLED = &H4
			SERVICE\_NO\_CHANGE = &HFFFFFFFF
		End Enum
 
		Public Enum SERVICE\_ERROR\_CONTROL As Int32
			SERVICE\_ERROR\_IGNORE = &H0
			SERVICE\_ERROR\_NORMAL = &H1
			SERVICE\_ERROR\_SEVERS = &H2
			SERVICE\_ERROR\_CRITICAL = &H3
			SERVICE\_NO\_CHANGE = &HFFFFFFFF
		End Enum
 
		<StructLayout(LayoutKind.Sequential)> \_
		Private Structure SERVICE\_DESCRIPTION
			<MarshalAs(UnmanagedType.LPTStr)> Dim lpDescription As String
		End Structure
 
		<StructLayout(LayoutKind.Sequential)> \_
		Public Structure QUERY\_SERVICE\_CONFIG
			Dim dwServiceType As SERVICE\_TYPE
			Dim dwStartType As SERVICE\_START\_TYPE
			Dim dwErrorControl As SERVICE\_ERROR\_CONTROL
			<MarshalAs(UnmanagedType.LPTStr)> Dim lpBinaryPathName As String
			<MarshalAs(UnmanagedType.LPTStr)> Dim lpLoadOrderGroup As String
			Dim dwTagId As Int32
			<MarshalAs(UnmanagedType.LPTStr)> Dim lpDependencies As String
			<MarshalAs(UnmanagedType.LPTStr)> Dim lpServiceStartName As String
			<MarshalAs(UnmanagedType.LPTStr)> Dim lpDisplayName As String
		End Structure
 
		Private Const SERVICE\_CONFIG\_DESCRIPTION As Int32 = 1
 
		Public Function InstallService(ByVal ServiceName As String, ByVal DisplayName As String, ByVal CommandLine As String, Optional ByVal ServiceType As SERVICE\_TYPE = SERVICE\_TYPE.SERVICE\_WIN32\_OWN\_PROCESS, Optional ByVal StartType As SERVICE\_START\_TYPE = SERVICE\_START\_TYPE.SERVICE\_DEMAND\_START, Optional ByVal Username As String = Nothing, Optional ByVal Password As String = Nothing) As Boolean
			Debug.Print("InstallService")
			Dim retval As Boolean = True
 
			Dim hSCManager As IntPtr = OpenSCManager(Nothing, Nothing, SCM\_ACCESS.SC\_MANAGER\_CREATE\_SERVICE)
			If hSCManager = 0 Then
				Debug.WriteLine("  OpenSCManager returned " & Marshal.GetLastWin32Error.ToString)
				retval = False
			Else
				Dim hService As IntPtr = CreateService(hSCManager, ServiceName, DisplayName, SERVICE\_ACCESS.SERVICE\_ALL\_RIGHTS, ServiceType, StartType, SERVICE\_ERROR\_CONTROL.SERVICE\_ERROR\_NORMAL, CommandLine, Nothing, Nothing, Nothing, Username, Password)
				If hService = 0 Then
					Debug.WriteLine("  CreateService returned " & Marshal.GetLastWin32Error.ToString)
					retval = False
				Else
					CloseServiceHandle(hService)
				End If
				CloseServiceHandle(hSCManager)
			End If
 
			Return retval
		End Function
 
		Public Function UninstallService(ByVal ServiceName As String) As Boolean
			Debug.WriteLine("UninstallService")
			Dim retval As Boolean = True
 
			Dim hSCManager As IntPtr = OpenSCManager(Nothing, Nothing, SCM\_ACCESS.SC\_MANAGER\_ALL\_ACCESS)
			If hSCManager = 0 Then
				Debug.WriteLine("  OpenSCManager returned " & Marshal.GetLastWin32Error.ToString)
				retval = False
			Else
				Dim hService As IntPtr = OpenService(hSCManager, ServiceName, SERVICE\_ACCESS.SERVICE\_ALL\_RIGHTS)
				If hService = 0 Then
					Debug.WriteLine("  OpenService returned " & Marshal.GetLastWin32Error.ToString)
					retval = False
				Else
					If DeleteService(hService) = False Then
						Debug.WriteLine("  DeleteService returned " & Marshal.GetLastWin32Error.ToString)
						retval = False
					End If
					CloseServiceHandle(hService)
				End If
				CloseServiceHandle(hSCManager)
			End If
 
			Return retval
		End Function
 
		Public Function GetServiceDescription(ByVal ServiceName As String) As String
			Debug.WriteLine("GetServiceDescription")
			Dim retval As String = ""
 
			Dim hSCManager As IntPtr = OpenSCManager(Nothing, Nothing, SCM\_ACCESS.SC\_MANAGER\_ALL\_ACCESS)
			Dim hService As IntPtr = OpenService(hSCManager, ServiceName, SERVICE\_ACCESS.SERVICE\_QUERY\_CONFIG)
			Dim hBuffer As IntPtr
			Dim iBytesNeeded As Integer = 0
			QueryServiceConfig2(hService, SERVICE\_CONFIG\_DESCRIPTION, hBuffer, iBytesNeeded, iBytesNeeded)
			hBuffer = Marshal.AllocHGlobal(iBytesNeeded)
			QueryServiceConfig2(hService, SERVICE\_CONFIG\_DESCRIPTION, hBuffer, iBytesNeeded, iBytesNeeded)
			Dim sdInfo As SERVICE\_DESCRIPTION = Marshal.PtrToStructure(hBuffer, GetType(SERVICE\_DESCRIPTION))
			retval = sdInfo.lpDescription
			Marshal.FreeHGlobal(hBuffer)
			CloseServiceHandle(hService)
			CloseServiceHandle(hSCManager)
			Return retval
		End Function
 
		Public Function GetServiceConfig(ByVal ServiceName As String) As QUERY\_SERVICE\_CONFIG
			Dim retval As QUERY\_SERVICE\_CONFIG
			Dim hSCManager As IntPtr = OpenSCManager(Nothing, Nothing, SCM\_ACCESS.SC\_MANAGER\_ALL\_ACCESS)
			Dim hService As IntPtr = OpenService(hSCManager, ServiceName, SERVICE\_ACCESS.SERVICE\_CHANGE\_CONFIG)
			Dim hBuffer As IntPtr
			Dim iBytesNeeded As Integer = 0
			QueryServiceConfig(hService, hBuffer, iBytesNeeded, iBytesNeeded)
			hBuffer = Marshal.AllocHGlobal(iBytesNeeded)
			QueryServiceConfig(hService, hBuffer, iBytesNeeded, iBytesNeeded)
			retval = Marshal.PtrToStructure(hBuffer, GetType(QUERY\_SERVICE\_CONFIG))
			Marshal.FreeHGlobal(hBuffer)
			CloseServiceHandle(hService)
			CloseServiceHandle(hSCManager)
			Return retval
		End Function
 
		Public Sub SetServiceDescription(ByVal ServiceName As String, ByVal SetDescription As String)
			Dim hSCManager As IntPtr = OpenSCManager(Nothing, Nothing, SCM\_ACCESS.SC\_MANAGER\_ALL\_ACCESS)
			Dim hService As IntPtr = OpenService(hSCManager, ServiceName, SERVICE\_ACCESS.SERVICE\_CHANGE\_CONFIG)
			Dim sdInfo As SERVICE\_DESCRIPTION
			sdInfo.lpDescription = SetDescription
			Dim hBuffer As IntPtr = Marshal.AllocHGlobal(Marshal.SizeOf(sdInfo))
			Marshal.StructureToPtr(sdInfo, hBuffer, False)
			ChangeServiceConfig2(hService, SERVICE\_CONFIG\_DESCRIPTION, hBuffer)
			Marshal.FreeHGlobal(hBuffer)
			CloseServiceHandle(hService)
			CloseServiceHandle(hSCManager)
		End Sub
 
		Public Sub SetServiceLogon(ByVal ServiceName As String, Optional ByVal NewUsername As String = Nothing, Optional ByVal NewPassword As String = Nothing)
			Dim hSCManager As IntPtr = OpenSCManager(Nothing, Nothing, SCM\_ACCESS.SC\_MANAGER\_ALL\_ACCESS)
			Dim hService As IntPtr = OpenService(hSCManager, ServiceName, SERVICE\_ACCESS.SERVICE\_CHANGE\_CONFIG)
			ChangeServiceConfig(hService, SERVICE\_TYPE.SERVICE\_NO\_CHANGE, SERVICE\_START\_TYPE.SERVICE\_NO\_CHANGE, SERVICE\_ERROR\_CONTROL.SERVICE\_NO\_CHANGE, Nothing, Nothing, Nothing, Nothing, NewUsername, NewPassword, Nothing)
			CloseServiceHandle(hService)
			CloseServiceHandle(hSCManager)
		End Sub
 
		Public Sub SetServiceDisplayName(ByVal ServiceName As String, ByVal NewDisplayName As String)
			Dim hSCManager As IntPtr = OpenSCManager(Nothing, Nothing, SCM\_ACCESS.SC\_MANAGER\_ALL\_ACCESS)
			Dim hService As IntPtr = OpenService(hSCManager, ServiceName, SERVICE\_ACCESS.SERVICE\_CHANGE\_CONFIG)
			ChangeServiceConfig(hService, SERVICE\_TYPE.SERVICE\_NO\_CHANGE, SERVICE\_START\_TYPE.SERVICE\_NO\_CHANGE, SERVICE\_ERROR\_CONTROL.SERVICE\_NO\_CHANGE, Nothing, Nothing, Nothing, Nothing, Nothing, Nothing, NewDisplayName)
			CloseServiceHandle(hService)
			CloseServiceHandle(hSCManager)
		End Sub
 
		Public Sub SetServiceType(ByVal ServiceName As String, ByVal NewType As SERVICE\_TYPE)
			Dim hSCManager As IntPtr = OpenSCManager(Nothing, Nothing, SCM\_ACCESS.SC\_MANAGER\_ALL\_ACCESS)
			Dim hService As IntPtr = OpenService(hSCManager, ServiceName, SERVICE\_ACCESS.SERVICE\_CHANGE\_CONFIG)
			ChangeServiceConfig(hService, NewType, SERVICE\_START\_TYPE.SERVICE\_NO\_CHANGE, SERVICE\_ERROR\_CONTROL.SERVICE\_NO\_CHANGE, Nothing, Nothing, Nothing, Nothing, Nothing, Nothing, Nothing)
			CloseServiceHandle(hService)
			CloseServiceHandle(hSCManager)
		End Sub
 
		Public Sub SetServiceStartType(ByVal ServiceName As String, ByVal NewStartType As SERVICE\_START\_TYPE)
			Dim hSCManager As IntPtr = OpenSCManager(Nothing, Nothing, SCM\_ACCESS.SC\_MANAGER\_ALL\_ACCESS)
			Dim hService As IntPtr = OpenService(hSCManager, ServiceName, SERVICE\_ACCESS.SERVICE\_CHANGE\_CONFIG)
			ChangeServiceConfig(hService, SERVICE\_TYPE.SERVICE\_NO\_CHANGE, NewStartType, SERVICE\_ERROR\_CONTROL.SERVICE\_NO\_CHANGE, Nothing, Nothing, Nothing, Nothing, Nothing, Nothing, Nothing)
			CloseServiceHandle(hService)
			CloseServiceHandle(hSCManager)
		End Sub
 
		Public Sub SetServiceCommandLine(ByVal ServiceName As String, ByVal NewCommandline As String)
			Dim hSCManager As IntPtr = OpenSCManager(Nothing, Nothing, SCM\_ACCESS.SC\_MANAGER\_ALL\_ACCESS)
			Dim hService As IntPtr = OpenService(hSCManager, ServiceName, SERVICE\_ACCESS.SERVICE\_CHANGE\_CONFIG)
			ChangeServiceConfig(hService, SERVICE\_TYPE.SERVICE\_NO\_CHANGE, SERVICE\_START\_TYPE.SERVICE\_NO\_CHANGE, SERVICE\_ERROR\_CONTROL.SERVICE\_NO\_CHANGE, NewCommandLine, Nothing, Nothing, Nothing, Nothing, Nothing, Nothing)
			CloseServiceHandle(hService)
			CloseServiceHandle(hSCManager)
		End Sub
	End Class
End Namespace
