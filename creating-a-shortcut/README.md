---
title: "Creating a Shortcut"
date: "2017-09-02"
categories: 
  - "powershell"
tags: 
  - "powershell"
---

$Desktop = \[Environment\]::GetFolderPath("Desktop")
$WshShell = New-Object -comObject WScript.Shell
$Shortcut = $WshShell.CreateShortcut("$Desktop\\Explorer.lnk")
$Shortcut.TargetPath = "explorer.exe"
$Shortcut.WorkingDirectory = "c:\\"
$Shortcut.IconLocation = "explorer.exe"
$Shortcut.Save()
