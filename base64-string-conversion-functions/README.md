---
title: "Base64 String Conversion Functions"
date: "2017-09-02"
categories: 
  - "powershell"
tags: 
  - "powershell"
---

Function Convert-StringToBase64 ( $Text ) {
    $enc = \[System.Text.Encoding\]::UTF8.GetBytes($text)
    \[System.Convert\]::ToBase64String($enc)
}
 
Function Convert-Base64ToString ( $Base64 ) {
    $enc = \[System.Convert\]::FromBase64String($Base64)
    \[System.Text.Encoding\]::UTF8.GetString($enc)
}
