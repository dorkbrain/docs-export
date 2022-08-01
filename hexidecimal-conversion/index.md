---
title: "Hexidecimal Conversion"
date: "2017-09-02"
categories: 
  - "powershell"
tags: 
  - "powershell"
---

#Decimal to Hex
\[Convert\]::ToString(255, 16)
 
#Hex to Decimal
\[Convert\]::ToInt32("0xFF", 16)
\[Convert\]::ToInt32("FF", 16)
