---
title: "Parse-IniFile"
date: "2017-09-02"
categories: 
  - "powershell"
tags: 
  - "powershell"
---

function Parse-IniFile (\[string\]$file)
{
  $ini = @{}
  switch -regex -file $file
  {
    #Section.
    "^\\\[(.+)\\\]$"
    {
      $section = $matches\[1\].Trim()
      $ini\[$section\] = @{}
      continue
    }
    #Int.
    "^\\s\*(\[^#\].+?)\\s\*=\\s\*(\\d+)\\s\*$"
    {
      $name,$value = $matches\[1..2\]
      $ini\[$section\]\[$name\] = \[int\]$value
      continue
    }
    #Decimal.
    "^\\s\*(\[^#\].+?)\\s\*=\\s\*(\\d+\\.\\d+)\\s\*$"
    {
      $name,$value = $matches\[1..2\]
      $ini\[$section\]\[$name\] = \[decimal\]$value
      continue
    }
    #Everything else.
    "^\\s\*(\[^#\].+?)\\s\*=\\s\*(.\*)"
    {
      $name,$value = $matches\[1..2\]
      $ini\[$section\]\[$name\] = $value.Trim()
    }
  }
  $ini
}
