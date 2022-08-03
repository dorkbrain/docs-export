---
title: "Tools.ExtractFileIcon"
date: "2017-09-03"
categories: 
  - "vb-net"
tags: 
  - "vb"
---

Imports System.IO
imports System.Drawing
Imports Microsoft.Win32
 
Namespace Tools
	Public Class ExtractFileIcon
		Private Declare Function ExtractIcon Lib "shell32.dll" Alias "ExtractIconA" (ByVal hIcon As IntPtr, ByVal lpszExeFileName As String, ByVal nIconIndex As Integer) As IntPtr
		Private Declare Function DestroyIcon Lib "user32" (ByVal hIcon As IntPtr) As Boolean
		Private hIcon As IntPtr = IntPtr.Zero
 
		Public Function GetIconFromFile(ByVal strFile As String) As Icon
			Dim myIcon As Icon
			Dim myFile As New FileInfo(strFile)
			Dim intIconIndex As Integer
			Dim strRegResult As String
			Dim str() As String
 
			If Not hIcon = IntPtr.Zero Then
				Try
					DestroyIcon(hIcon)
				Catch ex As Exception
				End Try
			End If
 
			Try ' If the file is an application, retrieve the icon from that file.
				If myFile.Extension.ToLower = ".exe" Then
					hIcon = ExtractIcon(IntPtr.Zero, myFile.FullName, 0)
					' If no success then use windows default.
					If hIcon.ToInt32 <= 1 Then hIcon = ExtractIcon(IntPtr.Zero, "Shell32.dll", 0)
				Else ' Get location from registry.
					strRegResult = Registry.ClassesRoot.OpenSubKey(myFile.Extension).GetValue("").ToString
					strRegResult = Registry.ClassesRoot.OpenSubKey(strRegResult).OpenSubKey("DefaultIcon").GetValue("").ToString
					str = strRegResult.Split(","c)
					If str.Length > 1 Then
						intIconIndex = CType(str(1), Integer)
					Else
						intIconIndex = 0
					End If
					strRegResult = str(0)
					hIcon = ExtractIcon(IntPtr.Zero, strRegResult, intIconIndex)
				End If
				myIcon = Icon.FromHandle(hIcon)
			Catch
				hIcon = ExtractIcon(IntPtr.Zero, "Shell32.dll", 0)
				myIcon = Icon.FromHandle(hIcon)
			End Try
			Return myIcon
		End Function
 
		Protected Overrides Sub Finalize()
			If Not hIcon = IntPtr.Zero Then
				Try
					DestroyIcon(hIcon)
				Catch ex As Exception
				End Try
			End If
 
			MyBase.Finalize()
		End Sub
	End Class
End Namespace
