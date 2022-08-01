---
title: "Randomize-HashTable"
date: "2017-09-02"
categories: 
  - "powershell"
tags: 
  - "powershell"
---

Function Randomize-HashTable (\[hashtable\]$HashTable) {
  $oldHash = $HashTable.clone()
  $newHash = New-Object System.Collections.Hashtable
  Do {
    $thisItem = ($oldHash.GetEnumerator() | Get-Random)
    $newHash.Add($thisItem.Key, $thisItem.Value)
    $oldHash.Remove($thisItem.Key)
  } While ( $oldHash.Count -gt 0 )
 
  $newHash
}
