---
title: "Get-DropBox"
date: "2017-09-02"
categories: 
  - "powershell"
tags: 
  - "powershell"
---

<#
 
.DESCRIPTION
 
Returns the path to the local drop box
 
#>
  $hostFile = Join-Path (Split-Path (Get-ItemProperty HKCU:\\Software\\Dropbox).InstallPath) "host.db"
  $encodedPath = \[System.Convert\]::FromBase64String((Get-Content $hostFile)\[1\])
  \[System.Text.Encoding\]::UTF8.GetString($encodedPath)
}
