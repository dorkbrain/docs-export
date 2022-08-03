---
title: "Tools.LineDrawing"
date: "2017-09-03"
categories: 
  - "vb-net"
tags: 
  - "vb"
---

Imports System.Runtime.InteropServices
 
Namespace Tools
	Public Class LineDrawing
	#Region " Character Code Enums "
		Class Chars
			<StructLayout(LayoutKind.Sequential)> Public Enum Block As Integer
				Light = 176     ' Lightly shaded
				Medium = 177    ' Medium shaded
				Dark = 178      ' Heavily shaded
				Solid = 219     ' Solid block

				Left = 221      ' Left half solid
				Right = 222     ' Right half solid
				Top = 223       ' Top half solid
				Bottom = 220    ' Bottom half solid
			End Enum
 
			<StructLayout(LayoutKind.Sequential)> Public Enum Line As Integer
				hSingle = 196   ' Horizontal Single Line
				vSingle = 179   ' Vertical Single Line
				hDouble = 205   ' Horizontal Double Line
				vDouble = 186   ' Vertical Double Line
			End Enum
 
			<StructLayout(LayoutKind.Sequential)> Public Enum Cross As Integer
				hSingle\_vSingle = 197   ' Horzontal=Single  Vertical=Single
				hDouble\_vDouble = 206   ' Horzontal=Double  Vertical=Double
				hSingle\_vDouble = 215   ' Horzontal=Single  Vertical=Double
				hDouble\_vSingle = 216   ' Horzontal=Double  Vertical=Single
			End Enum
 
			<StructLayout(LayoutKind.Sequential)> Public Enum Corner As Integer
				tl\_hSingle\_vSingle = 218    ' Top-Left - Horizontal=Single  Vertical=Single
				tr\_hSingle\_vSingle = 191    ' Top-Right - Horizontal=Single  Vertical=Single
				bl\_hSingle\_vSingle = 192    ' Bottom-Left - Horizontal=Single  Vertical=Single
				br\_hSingle\_vSingle = 217    ' Bottom-Right - Horizontal=Single  Vertical=Single

				tl\_hDouble\_vDouble = 201    ' Top-Left - Horizontal=Double  Vertical=Double
				tr\_hDouble\_vDouble = 187    ' Top-Right - Horizontal=Double  Vertical=Double
				bl\_hDouble\_vDouble = 200    ' Bottom-Left - Horizontal=Double  Vertical=Double
				br\_hDouble\_vDouble = 188    ' Bottom-Right - Horizontal=Double  Vertical=Double

				tl\_hSingle\_vDouble = 214    ' Top-Left - Horizontal=Single  Vertical=Double
				tr\_hSingle\_vDouble = 183    ' Top-Right - Horizontal=Single  Vertical=Double
				bl\_hSingle\_vDouble = 211    ' Bottom-Left - Horizontal=Single  Vertical=Double
				br\_hSingle\_vDouble = 189    ' Bottom-Right - Horizontal=Single  Vertical=Double

				tl\_hDouble\_vSingle = 213    ' Top-Left - Horizontal=Double  Vertical=Single
				tr\_hDouble\_vSingle = 184    ' Top-Right - Horizontal=Double  Vertical=Single
				bl\_hDouble\_vSingle = 212    ' Bottom-Left - Horizontal=Double  Vertical=Single
				br\_hDouble\_vSingle = 190    ' Bottom-Right - Horizontal=Double  Vertical=Single
			End Enum
 
			<StructLayout(LayoutKind.Sequential)> Public Enum Intersect As Integer
				t\_hSingle\_vSingle = 194     'Top - Horizontal=Single  Vertical=Single
				b\_hSingle\_vSingle = 193     'Bottom - Horizontal=Single  Vertical=Single
				l\_hSingle\_vSingle = 195     'Left - Horizontal=Single  Vertical=Single
				r\_hSingle\_vSingle = 180     'Right - Horizontal=Single  Vertical=Single

				t\_hDouble\_vDouble = 203     'Top - Horizontal=Double  Vertical=Double
				b\_hDouble\_vDouble = 202     'Bottom - Horizontal=Double  Vertical=Double
				l\_hDouble\_vDouble = 204     'Left - Horizontal=Double  Vertical=Double
				r\_hDouble\_vDouble = 185     'Right - Horizontal=Double  Vertical=Double

				t\_hSingle\_vDouble = 210     'Top - Horizontal=Single  Vertical=Double
				b\_hSingle\_vDouble = 208     'Bottom - Horizontal=Single  Vertical=Double
				l\_hSingle\_vDouble = 198     'Left - Horizontal=Single  Vertical=Double
				r\_hSingle\_vDouble = 181     'Right - Horizontal=Single  Vertical=Double

				t\_hDouble\_vSingle = 209     'Top - Horizontal=Double  Vertical=Single
				b\_hDouble\_vSingle = 207     'Bottom - Horizontal=Double  Vertical=Single
				l\_hDouble\_vSingle = 199     'Left - Horizontal=Double  Vertical=Single
				r\_hDouble\_vSingle = 182     'Right - Horizontal=Double  Vertical=Single
			End Enum
		End Class
	#End Region
 
	#Region " Private API Declares "
		' Gets the handle of the StdOutput
		Private Declare Function GetStdHandle Lib "kernel32.dll" (ByVal nStdHandle As Int32) As Int32
 
		' Writes text to the console
		Private Declare Function WriteConsole Lib "kernel32.dll" Alias "WriteConsoleA" (ByVal hConsoleOutput As Int32, ByVal lpBuffer As String, ByVal nNumberOfCharsToWrite As Int32, ByRef lpNumberOfCharsWritten As Int32, ByRef lpReserved As Int32) As Int32
 
		' Set the cursor position in the console
		Private Declare Function SetConsoleCursorPosition Lib "kernel32" (ByVal hConsoleOutput As Int32, ByVal dwCursorPosition As COORD) As Boolean
 
		' Used to get the current cursor position in the console
		Private Declare Function GetConsoleScreenBufferInfo Lib "kernel32" (ByVal hconsoleoutput As IntPtr, ByRef lpConsoleScreenBufferInfo As CONSOLE\_SCREEN\_BUFFER\_INFO) As Boolean
	#End Region
 
	#Region " Private Constants and Structures "
		Private Const STD\_OUTPUT\_HANDLE As Int32 = -11&
 
		' Used by GetConsoleScreenBufferInfo API call
		<StructLayout(LayoutKind.Sequential)> Private Structure CONSOLE\_SCREEN\_BUFFER\_INFO
			Public dwSize As COORD
			Public dwCursorPosition As COORD
			Public wAttributes As Short
			Public srWindow As SMALL\_RECT
			Public dwMaximumWindowSize As COORD
		End Structure
 
		' Used by CONSOLE\_SCREEN\_BUFFER\_INFO
		<StructLayout(LayoutKind.Sequential)> Private Structure SMALL\_RECT
			Public Left As Short
			Public Top As Short
			Public Right As Short
			Public Bottom As Short
		End Structure
 
		' Used by CONSOLE\_SCREEN\_BUFFER\_INFO
		<StructLayout(LayoutKind.Sequential)> Private Structure COORD
			Public X As Short
			Public Y As Short
		End Structure
	#End Region
 
	#Region " Public Subs and Functions "
		Public Sub DrawASCII(ByVal ascii As Integer, Optional ByVal length As Integer = 1, Optional ByVal cursorTop As Integer = -1, Optional ByVal cursorLeft As Integer = -1)
			' Handle to StdOut
			Dim iOutputHandle As Int32 = GetStdHandle(STD\_OUTPUT\_HANDLE)
 
			If cursorTop >= 0 Or cursorLeft >= 0 Then
				' Get Console Info (used to get current cursor location)
				Dim csbi As CONSOLE\_SCREEN\_BUFFER\_INFO
				GetConsoleScreenBufferInfo(iOutputHandle, csbi)
				Dim posCursor As COORD = csbi.dwCursorPosition
 
				' Set Cursor Position
				If Not (cursorTop = -1) Then posCursor.Y = cursorTop
				If Not (cursorLeft = -1) Then posCursor.X = cursorLeft
				SetConsoleCursorPosition(iOutputHandle, posCursor)
			End If
 
			' Fill the output buffer
			Dim outBuffer As String = StrDup(length, Chr(ascii))
			Dim outLen As Int32 = outBuffer.Length
			Dim retval As Int32 = 0
 
			' Write text to the console
			WriteConsole(iOutputHandle, outBuffer, outLen, retval, 0)
		End Sub
	#End Region
	End Class
End Namespace
