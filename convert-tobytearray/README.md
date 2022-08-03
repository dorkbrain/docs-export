---
title: "Convert-ToByteArray"
date: "2017-09-02"
categories: 
  - "powershell"
tags: 
  - "powershell"
---

Function Convert-ToByteArray ( \[string\]$Text ) {
  \[System.Text.Encoding\]::UTF8.GetBytes($Text)
}
