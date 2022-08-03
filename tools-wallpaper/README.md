---
title: "Tools.Wallpaper"
date: "2017-09-03"
categories: 
  - "vb-net"
tags: 
  - "vb"
---

Imports System.IO
 
Namespace Tools
	Public Class Wallpaper
		Const SPI\_SETDESKWALLPAPER As Integer = 20
		Const SPIF\_UPDATEINIFILE As Integer = &H1
		Const SPIF\_SENDWININICHANGE As Integer = &H2
 
		Private Declare Function SystemParametersInfo Lib "user32" Alias "SystemParametersInfoA" (ByVal uAction As Integer, ByVal uParam As Integer, ByVal lpvParam As String, ByVal fuWinIni As Integer) As Integer
 
		Private \_RegKey As Microsoft.Win32.RegistryKey
		Private \_PictureBmp As String
		Private \_imgPicture As Image
 
		Public Shared Enum WallpaperStyle
			Centered = 0
			Tiled = 1
			Stretched = 2
		End Enum
 
		Public Function Shared Change(ByVal PicturePath As String, Optional ByVal wpStyle As WallpaperStyle = WallpaperStyle.Centered) As Boolean
			If PicturePath Is Nothing Then Return False ' No picture specfied

			If PicturePath = "" Then
				SystemParametersInfo(SPI\_SETDESKWALLPAPER, 0, PicturePath, SPIF\_UPDATEINIFILE Or SPIF\_SENDWININICHANGE)
				Return True
			End If
 
			If File.Exists(PicturePath) Then ' Does the file actually exist?
				Try
					' This class doesn't use my registry class so it can remain independant.
					\_RegKey = Microsoft.Win32.Registry.CurrentUser.OpenSubKey("Control Panel\\Desktop", True)
					Select Case wpStyle
						Case WallpaperStyle.Centered
							\_RegKey.SetValue("TileWallpaper", "0")
							\_RegKey.SetValue("WallpaperStyle", "0")
						Case WallpaperStyle.Tiled
							\_RegKey.SetValue("TileWallpaper", "1")
							\_RegKey.SetValue("WallpaperStyle", "0")
						Case WallpaperStyle.Stretched
							\_RegKey.SetValue("TileWallpaper", "0")
							\_RegKey.SetValue("WallpaperStyle", "2")
					End Select
					\_RegKey.Flush()
					\_RegKey.Close()
					' If picture not a bitmap then convert it first.
					Dim fi As New FileInfo(PicturePath)
					If fi.Extension.ToLower <> ".bmp" Then
						\_imgPicture = Image.FromFile(PicturePath, True)
						\_PictureBmp = PicturePath.Substring(0, PicturePath.Length - 3) & "bmp"
						\_imgPicture.Save(\_PictureBmp, System.Drawing.Imaging.ImageFormat.Bmp)
					Else
						\_PictureBmp = PicturePath
					End If
					' Once bitmap, set the the wallpaper.
					SystemParametersInfo(SPI\_SETDESKWALLPAPER, 0, \_PictureBmp, SPIF\_UPDATEINIFILE Or SPIF\_SENDWININICHANGE)
					Return True
				Catch ex As Exception
					Return False
				End Try
			Else
				Return False
			End If
		End Function
	End Class
End Namespace
