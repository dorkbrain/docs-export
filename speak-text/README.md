---
title: "Speak-Text"
date: "2017-09-02"
categories: 
  - "powershell"
tags: 
  - "powershell"
---

Function Speak-Text () 
{
  param(
    \[CmdletBinding()\]
    \[Parameter(Position = 0, ValueFromPipeline = $true)\] 
    \[string\]$Text = "",
    \[switch\]$Wait
  )
 
 
  begin 
  {
    $flags = 1
    If ($Wait) { $flags = 0 }
    $voice = New-Object -ComObject SAPI.SpVoice
  }
 
  process
  {
    If ($PSBoundParameters\['Verbose'\]) { $Text }
    If ($Text) { \[void\] $voice.Speak($Text, $flags) }
  }
 
  end
  {
    $voice = ""
  }
 
<#
  .SYNOPSIS
  Uses SAPI to convert text to speech.
  .PARAMETER Text
  The text to convert to speech.
  .PARAMETER Wait
  Wait for speech to finish before passing continuing the script.
  .EXAMPLE
  Speak-Text "This is a test." -Wait
  Converts text to speech.  Waits for speech to finish before giving control back.
  .EXAMPLE
  Get-Content "c:\\textfile.txt" | Speak-Text -Wait -Verbose
  Reads the contents of a text file and converts them to speech.  Each line is displayed as it's being read.
#>  
}
Set-Alias Speak Speak-Text
