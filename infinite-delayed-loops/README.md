---
title: "Infinite Delayed Loops"
date: "2017-09-02"
categories: 
  - "powershell"
tags: 
  - "powershell"
---

**Execute code block then delay 3 seconds:**

Do {
  # Display top 10 processes by memory consumption
  Get-Process | Sort-Object -Property PrivateMemorySize -Descending | Select-Object -Property @{Label="Private Memory"; Expression={($\_.PrivateMemorySize/1MB).ToString("N2") + " MB"}}, @{Label="Processor Time"; Expression={$\_.CPU.ToString("N2") + " sec"}}, ID, ProcessName -First 10 | Format-Table -AutoSize
} Until ( Start-Sleep -Seconds 3 )

This works because _Do..Until_ keeps looping until the expression between ( and ) evaluate to _$true_. Since _Start-Sleep_ doesn't return any value it will never equal _$true_ thus creating the infinite loop.

 

**Delay 3 seconds then execute code block:**

While ( -not (Start-Sleep -Seconds 3) ) {
  # Display top 10 processes by memory consumption
  Get-Process | Sort-Object -Property PrivateMemorySize -Descending | Select-Object -Property @{Label="Private Memory"; Expression={($\_.PrivateMemorySize/1MB).ToString("N2") + " MB"}}, @{Label="Processor Time"; Expression={$\_.CPU.ToString("N2") + " sec"}}, ID, ProcessName -First 10 | Format-Table -AutoSize
}

This works because _\-not_ turns the empty response from _Start-Sleep_ into a _$true_. Since this will never change we get our infinite loop. This is the same as the _Do..Until_ example above except _Start-Sleep_ is executed before the code block instead of after.
