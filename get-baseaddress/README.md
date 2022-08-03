---
title: "Get-BaseAddress"
date: "2017-09-02"
categories: 
  - "powershell"
tags: 
  - "powershell"
---

Function Get-BaseAddress ( $ImageName ) {
  Get-Process $ImageName | ForEach-Object {
    $\_.Modules | ForEach-Object {
      New-Object PsObject -Property @{
        ModuleName = $\_.ModuleName
        BaseAddress = \[Convert\]::ToString(\[Convert\]::ToInt64($\_.BaseAddress, 10),16)
      }
    }
  }
}
