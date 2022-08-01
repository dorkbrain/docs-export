---
title: "Some Raw Console Properties"
date: "2017-09-03"
categories: 
  - "powershell"
tags: 
  - "powershell"
---

\# Set cursor position
\[Console\]::SetCursorPosition($X, $Y)
 
# Get console width and height
$conW = $Host.UI.RawUI.WindowSize.Width
$conH = $Host.UI.RawUI.WindowSize.Height
 
# Hide the blinking cursor
\[Console\]::CursorVisible = $False
 
# Change the console window title
$Host.UI.RawUI.WindowTitle = "New title"
