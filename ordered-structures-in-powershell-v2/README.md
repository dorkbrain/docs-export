---
title: "Ordered Structures in PowerShell v2"
date: "2017-09-02"
categories: 
  - "powershell"
tags: 
  - "powershell"
---

**The VB way:**

\# Create a custom ordered structure in VB.Net
Add-Type -Language VisualBasic @"
Imports System.Runtime.InteropServices
<StructLayout(LayoutKind.Sequential)> Public Structure VarStruct
	Public Name As String
	Public Value As String
	Public Visibility As String
End Structure
"@

Get-ChildItem Variable:\\ | ForEach-Object {
  # Create a new instance of the ordered structure
  $thisVar = New-Object VarStruct
  
  # Assign values to the properties
  $thisVar.Name = "$" + $\_.Name
  $thisVar.Value = $\_.Value
  $thisVar.Visibility = $\_.Visibility
  
  # Return the ordered structure
  $thisVar
}

 

**Pure Powershell:**

\# Define the structure order
$properties = @(
                "Name"
                "Value"
                "Visibility"
              )

Get-ChildItem Variable:\\ | ForEach-Object {
  # Build the actual object and pipe it to Select-Object to get the order correct
  New-Object PSObject -Property @{ 
    Name = "$" + $\_.Name
    Value = $\_.Value
    Visibility = $\_.Visibility
  } | Select-Object -Property $properties 
}

 

**In v3 and up you can simply create an ordered hash table:**

<sxh powershell>Get-ChildItem Variable:\\ | ForEach-Object {
  \[PSCustomObject\]\[ordered\]@{
    Name = "$" + $\_.Name
    Value = $\_.Value
    Visibility = $\_.Visibility
  }
}
