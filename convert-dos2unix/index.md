---
title: "Convert-Dos2Unix"
date: "2017-09-02"
categories: 
  - "powershell"
tags: 
  - "powershell"
---

Function Convert-Dos2Unix() {
  \[CmdletBinding()\]
  Param(
    \[Parameter(Position=0,Mandatory=$true,ValueFromPipeline=$true)\]
    $inFile,
    \[Parameter(Position=1,Mandatory=$false)\]
    $outFile = $infile
  )
  
  Begin {
    $utf8 = New-Object System.Text.UTF8Encoding $false
  }
  
  Process {
    $contents = \[IO.File\]::ReadAllText($infile) -Replace "\`r\`n?", "\`n"
    \[IO.File\]::WriteAllText($outFile, $contents, $utf8)
  }  
}
