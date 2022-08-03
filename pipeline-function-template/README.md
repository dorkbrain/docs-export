---
title: "Pipeline Function Template"
date: "2017-09-02"
categories: 
  - "powershell"
tags: 
  - "powershell"
---

Function Pipeline-Function {
  \[CmdletBinding()\]
  Param (
    \[Parameter(Position=0,Mandatory=$true,ValueFromPipeline=$true)\]
    $inData,
    \[Parameter(Position=1)\]
    \[ValidateSet("Abc", "deF")\]
    $myType
  )
 
  Begin {
    "Begin block"
  }
 
  Process {
    "Process block"
    "  {0}" -f $inData
  }
 
  End {
    "End Block"
    "  {0}" -f $myType
  }
}
