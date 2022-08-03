---
title: "Update-GAC"
date: "2017-09-02"
categories: 
  - "powershell"
tags: 
  - "powershell"
---

Function Update-GAC {
  Param(
    \[switch\]$Force  
  )
  
  Set-Alias ngen (Join-Path (\[System.Runtime.InteropServices.RuntimeEnvironment\]::GetRuntimeDirectory()) ngen.exe)
  \[AppDomain\]::CurrentDomain.GetAssemblies() | Sort-Object {Split-path $\_.location -leaf} | ForEach-Object {
    $Name = (Split-Path $\_.location -leaf)
    if ((-Not \[System.Runtime.InteropServices.RuntimeEnvironment\]::FromGlobalAccessCache($\_)) -or $Force) {
      Write-Host "NGEN: $Name"
      ngen $\_.location | %{"\`t$\_"}
    } else {
      Write-Host "Already NGEN'd: $Name"
    }
  }
}
