---
title: "Hot Keys for Console Windows"
date: "2017-09-02"
categories: 
  - "autohotkey"
tags: 
  - "ahk"
  - "autohotkey"
---

<table><tbody><tr><td>Ctrl+V</td><td>Paste clipboard text</td></tr><tr><td>Ctrl+D</td><td>Exit console</td></tr><tr><td>Ctrl+L</td><td>Clear the screen</td></tr></tbody></table>

#IfWinActive ahk\_class ConsoleWindowClass
	^V::SendInput {Raw}%clipboard%
	^D::SendInput {ESC}exit{Enter}
	^L::SendInput {ESC}cls{Enter}
#IfWinActive
