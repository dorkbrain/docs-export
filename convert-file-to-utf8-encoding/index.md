---
title: "Convert File to UTF8 Encoding"
date: "2017-09-02"
categories: 
  - "powershell"
tags: 
  - "powershell"
---

Get-ChildItem \*.txt | ForEach-Object { (Get-Content $\_) | Out-File -Encoding UTF8 $\_ }
