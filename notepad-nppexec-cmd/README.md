---
title: "Notepad++ nppexec.cmd"
date: "2017-08-27"
categories: 
  - "windows"
tags: 
  - "notepad"
  - "windows"
---

I use this batch file to execute other types of scripts from within Notepad++. Additional file types can easily be added with more :CASE.xxx blocks.

@echo off
:: Configure notepad++ to run this command:
::   c:\\path\\to\\nppexec.cmd "$(FULL\_CURRENT\_PATH)"

if "%~f1" == "" (
  echo This script requires exactly one command line argument!
  echo Press any key to exit  . . . && pause > nul
  exit /b
)
echo "%~f1"
echo.
set ext=%~x1
2>nul call :CASE%ext% %1
if %ERRORLEVEL%==1 call :CASE.DEFAULT
echo.
echo Press any key to exit . . . && pause > nul
exit /b

:CASE.BAT
:CASE.CMD
  set errorlevel=0
  pushd "%~p1"
  echo Starting Batch script . . .
  cmd /c "%~f1"
  popd
  goto :EOF

:CASE.PS1
  set errorlevel=0
  pushd "%~p1"
  echo Starting PowerShell script . . .
  powershell -executionpolicy bypass -noprofile -nologo -file "%~f1"
  popd
  goto :EOF
  
:CASE.AHK
  set errorlevel=0
  pushd "%~p1"
  echo Starting AutoHotkey script . . .
  C:\\MyFiles\\\_AHK\_\\AutoHotkey.exe "%~f1"
  popd
  goto :EOF
  
:CASE.DEFAULT
  color 0c
  echo Don't know what to do with a %ext% file!
  echo Edit "%~f0" to add support for more file types.
  goto :EOF
