---
title: "Force Script to Run in 32-bit"
date: "2017-09-02"
categories: 
  - "powershell"
tags: 
  - "powershell"
---

if ($env:Processor\_Architecture -ne "x86") {
  # relaunch in 32-bit
  &"$env:WINDIR\\syswow64\\windowspowershell\\v1.0\\powershell.exe" -NonInteractive -NoProfile $myInvocation.Line
  exit
}
