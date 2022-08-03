---
title: "Randomize Windows Lock Screens"
date: "2017-09-03"
categories: 
  - "powershell"
tags: 
  - "powershell"
---

\# $repoDir stores the path to the lock screens to choose from.
# Files must be in JPG format and less than 256KB in size.
 
$repoDir = "C:\\LockScreens"
 
 
### DO NOT MODIFY BELOW THIS LINE ###
 
$EventLogSource = "LockScreen"
If (!\[System.Diagnostics.EventLog\]::SourceExists($EventLogSource)) {
  \[System.Diagnostics.EventLog\]::CreateEventSource($EventLogSource, "Application")
}
 
If (! (Test-Path $repoDir) ) {
  New-Item -ItemType Directory $repoDir | Out-Null
}
 
If (! (Test-Path "C:\\windows\\system32\\oobe\\info\\backgrounds" ) ) {
  New-Item -ItemType Directory "C:\\windows\\system32\\oobe\\info\\backgrounds" | Out-Null
}
 
Get-ChildItem $(Join-Path $repoDir "\*.jpg") | Where-Object { $\_.Length -le 256KB } | Get-Random | ForEach-Object {
  Write-EventLog -Message "New lock screen copied: $($\_.FullName)" -Source $EventLogSource -LogName "Application" -EntryType "Information" -EventId "0"
  Copy-Item $\_ "C:\\windows\\system32\\oobe\\info\\backgrounds\\backgroundDefault.jpg" -Force
  Set-ItemProperty "HKLM:\\SOFTWARE\\Policies\\Microsoft\\Windows\\System" -Name "UseOEMBackground" -Value 1 -Type DWord
}
