---
title: "Randomize an Array or List of Objects"
date: "2017-09-03"
categories: 
  - "powershell"
tags: 
  - "powershell"
---

You can pipe the contents to Sort-Object {Get-Random}. This does not seem to work for Hash Tables.

Get-ChildItem C:\\ | Sort-Object {Get-Random}
