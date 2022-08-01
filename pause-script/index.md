---
title: "Pause-Script"
date: "2017-09-02"
categories: 
  - "powershell"
tags: 
  - "powershell"
---

Function Pause-Script ($Prompt = "Press any key to continue . . .") {
  Write-Host -NoNewLine "$Prompt"
  \[void\]$host.UI.RawUI.ReadKey("NoEcho,IncludeKeyDown")
}
