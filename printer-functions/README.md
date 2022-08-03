---
title: "Printer Functions"
date: "2017-09-02"
categories: 
  - "powershell"
tags: 
  - "powershell"
---

Function Get-Printer (\[string\]$PrinterPath = "") {
  If ($PrinterPath -eq "") {
    # Get all printers
    Get-WmiObject -class Win32\_Printer
  } Else {
    # Get a single printer based on it's name
    Get-WmiObject -class Win32\_Printer | Where-Object {$\_.Name -eq $PrinterPath}
  }
}
 
Function Map-Printer (\[string\]$PrinterPath) {
  # Add a printer
  $(New-Object -com WScript.Network).AddWindowsPrinterConnection($PrinterPath)
  Get-Printer $PrinterPath
}
 
Function Set-DefaultPrinter {
  \[CmdletBinding()\]
  param(\[Parameter(ValueFromPipeline=$true)\]$Printer)
  begin { $PipelineInput = -not $PSBoundParameters.ContainsKey("item") }
  process { 
    If ($PipelineInput) {
      $Printer.SetDefaultPrinter() | Out-Null
      $Printer
    } Else {
      $PrinterParam = Get-Printer $Printer
      $PrinterParam.SetDefaultPrinter() | Out-Null
      $PrinterParam
    }
  }
  end { }
}
 
Function Remove-Printer {
  \[CmdletBinding()\]
  param(\[Parameter(ValueFromPipeline=$true)\]$Printer)
  begin { $PipelineInput = -not $PSBoundParameters.ContainsKey("item") }
  process { 
    If ($PipelineInput) {
      $Printer.psbase.Delete() | Out-Null
    } Else {
      $PrinterParam = Get-Printer $Printer
      $PrinterParam.psbase.Delete() | Out-Null
    }
  }
  end { }
}
