---
title: "Testing if a Web Page is Reachable"
date: "2017-09-03"
categories: 
  - "powershell"
tags: 
  - "powershell"
---

If ( $PSVersionTable.PSVersion.Major -lt 3 ) {
  ""
  "PowerShell v3 or higher is required.  You can download v3 from"
  "http://www.microsoft.com/en-us/download/details.aspx?id=34595"
  "as part of the Windows Management Framework."
  ""
  Pause
  Exit
}
 
$interval = 15
$destination = "http://google.com"
 
"Performing download test every {0} seconds..." -f $interval
Do {
  Try {
    Invoke-WebRequest $destination | Out-Null
    Write-Host (" {0} - OK " -f (Get-Date).ToString()) -ForegroundColor White
  } Catch {
    Write-Host (" {0} - ERROR " -f (Get-Date).ToString()) -ForegroundColor White -BackgroundColor DarkRed
  }
  Start-Sleep -Seconds $interval
} While ( $true )
