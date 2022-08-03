---
title: "Read a Large Text File Line-by-Line"
date: "2017-09-03"
categories: 
  - "powershell"
tags: 
  - "powershell"
---

$bigfile = \[System.IO.File\]::OpenText("c:\\bigfile.txt")
Do {
    $thisLine = $bigfile.ReadLine()
} Until ( $bigfile.EndOfStream )
