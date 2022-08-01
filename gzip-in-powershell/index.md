---
title: "GZip in Powershell"
date: "2017-09-02"
categories: 
  - "powershell"
tags: 
  - "powershell"
---

Function ConvertTo-GZipString () {
<#
  .SYNOPSIS
    Compresses a string with the GZip algorithm 
    
  .DESCRIPTION
    Compresses a string with the GZip algorithm and returns the result in a Base64 string
    
  .PARAMETER String
    Any plain text string to be compressed
    
  .EXAMPLE
    dir | Out-String | ConvertTo-GZipString
    
  .LINK
    ConvertFrom-GZipString
#>

  \[CmdletBinding()\]
  Param(
    \[Parameter(Mandatory=$True,ValueFromPipeline=$True,ValueFromPipelinebyPropertyName=$True)\]
    $String
  )

  Process {
    $String | ForEach-Object {
      $ms = New-Object System.IO.MemoryStream
      $cs = New-Object System.IO.Compression.GZipStream($ms, \[System.IO.Compression.CompressionMode\]::Compress)
      $sw = New-Object System.IO.StreamWriter($cs)
      $sw.Write($\_)
      $sw.Close()
      \[System.Convert\]::ToBase64String($ms.ToArray())   
    }
  }
}

Function ConvertFrom-GZipString () {
<#
  .SYNOPSIS
    Decompresses a Base64 GZipped string
    
  .DESCRIPTION
    Decompresses a Base64 GZipped string
    
  .PARAMETER String
    A Base64 encoded GZipped string
    
  .EXAMPLE
    $compressedString | ConvertFrom-GZipString
    
  .LINK
    ConvertTo-GZipString
#>

  \[CmdletBinding()\]
  Param(
    \[Parameter(Mandatory=$True,ValueFromPipeline=$True,ValueFromPipelinebyPropertyName=$True)\]
    $String
  )

  Process {
    $String | ForEach-Object {
      $compressedBytes = \[System.Convert\]::FromBase64String($\_)
      $ms = New-Object System.IO.MemoryStream
      $ms.write($compressedBytes, 0, $compressedBytes.Length)
      $ms.Seek(0,0) | Out-Null
      $cs = New-Object System.IO.Compression.GZipStream($ms, \[System.IO.Compression.CompressionMode\]::Decompress)
      $sr = New-Object System.IO.StreamReader($cs)
      $sr.ReadToEnd()
    }
  }
}
