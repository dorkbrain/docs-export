---
title: "RightOf and LeftOf Functions"
date: "2017-09-02"
categories: 
  - "powershell"
tags: 
  - "powershell"
---

Function Get-LeftOf() {
  \[CmdletBinding()\]
  Param(
    \[Parameter(Position=0, Mandatory=$true, ValueFromPipeline=$true)\]
    \[string\]$Text,
    \[Parameter(Position=1, Mandatory=$false, ValueFromPipeline=$false)\]
    \[string\]$Find
  )
  
  Process {
    $\_ | ForEach-Object {
      If ($\_.ToUpper().IndexOf($Find.ToUpper()) -le 0) {
        $\_
      } Else {
        $\_.Substring(0,$\_.ToUpper().IndexOf($Find.ToUpper()))
      }
    }
  }
}

Function Get-LeftOfLast() {
  \[CmdletBinding()\]
  Param(
    \[Parameter(Position=0, Mandatory=$true, ValueFromPipeline=$true)\]
    \[string\]$Text,
    \[Parameter(Position=1, Mandatory=$false, ValueFromPipeline=$false)\]
    \[string\]$Find
  )  
  
  Process {
    $\_ | forEach-Object {
      If ($\_.ToUpper().LastIndexOf($Find.ToUpper()) -le 0) {
        $\_
      } Else {
        $\_.Substring(0,$\_.ToUpper().LastIndexOf($Find.ToUpper()))
      }
    }
  }
}

Function Get-RightOf() {
  \[CmdletBinding()\]
  Param(
    \[Parameter(Position=0, Mandatory=$true, ValueFromPipeline=$true)\]
    \[string\]$Text,
    \[Parameter(Position=1, Mandatory=$false, ValueFromPipeline=$false)\]
    \[string\]$Find
  )  
  
  Process {
    $\_ | forEach-Object {
      If ($\_.ToUpper().IndexOf($Find.ToUpper()) -le 0) {
        $\_
      } Else {
        $\_.Substring($\_.ToUpper().IndexOf($Find.ToUpper())+$Find.Length)
      }
    }
  }  
}

Function Get-RightOfLast() {
  \[CmdletBinding()\]
  Param(
    \[Parameter(Position=0, Mandatory=$true, ValueFromPipeline=$true)\]
    \[string\]$Text,
    \[Parameter(Position=1, Mandatory=$false, ValueFromPipeline=$false)\]
    \[string\]$Find
  )  
  
  Process {
    $\_ | forEach-Object {
      If ($\_.ToUpper().LastIndexOf($Find.ToUpper()) -le 0) {
        $\_
      } Else {
        $\_.Substring($\_.ToUpper().LastIndexOf($Find.ToUpper())+$Find.Length)
      }
    }
  }  
}
