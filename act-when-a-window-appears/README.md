---
title: "Act When a Window Appears"
date: "2017-09-02"
categories: 
  - "autohotkey"
tags: 
  - "ahk"
  - "autohotkey"
---

I wrote this to autologin to my VMware vSphere Client. Every 500ms it checks to see if my window is open. If it finds a window that I'm interested in it acts, then waits for the window to close. I can easily add additional windows with more IF statements.

#SingleInstance Force
#Persistent

; Trigger on window load
SetTimer, OnWindowLoad, 500
Return

; Subroutine OnWindowLoad
OnWindowLoad:
	; VMware vSphere Client - Auto Login
	If WinExist("ahk\_class WindowsForms10.Window.8.app.0.259f9d2") {
		WinActivate
		WinWaitActive
		Sleep, 500
		SendInput !n^a
		SendInput IP ADDRESS GOES HERE
		SendInput !u^a
		SendInput USER NAME GOES HERE
		SendInput !p^a
		SendInput {Raw}PASSWORD GOES HERE
		SendInput !l
		WinWaitClose
		Return
	}
Return
