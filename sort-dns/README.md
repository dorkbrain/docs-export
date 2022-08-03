---
title: "Sort-DNS"
date: "2017-11-16"
categories: 
  - "powershell"
tags: 
  - "powershell"
---

This will take a list of DNS domains and sort them properly with the TLD being the most significant and the left-most subdomain being the least significant.  I reverse the order of the domain, sort the array, then reverse the order again.

Function Sort-DNS {
  Param (
    \[Parameter(Position=0,Mandatory=$true)\]
    \[string\[\]\]$Domains
  )

  $reversed = @()
  $sorted = @()
  foreach ($domain in $domains) {
    $splitdomain = $domain.Split(".")
    \[array\]::Reverse($splitdomain)
    $reversed += $splitdomain -Join "."
  }
  $reversed = $reversed | Sort-Object
  foreach ($domain in $reversed) {
    $splitdomain = $domain.Split(".")
    \[array\]::Reverse($splitdomain)
    $sorted += $splitdomain -Join "."
  }
  $sorted
}

Example:

$myDomains = @(
  "www.google.com"
  "google.com"
  "images.google.com"
  "www.microsoft.com"
  "microsoft.com"
)
Sort-DNS $myDomains

Output:

google.com
images.google.com
www.google.com
microsoft.com
www.microsoft.com
