---
title: "Get-DriveInfo"
date: "2017-09-02"
categories: 
  - "powershell"
tags: 
  - "powershell"
---

Function Get-DriveInfo () {
<#
.DESCRIPTION
Gets drive info from a computer.

.PARAMETER DriveLetter
The letter of the drive you want to get info for.

.PARAMETER MeasureUnit
Show sizes in this unit of measure.  Valid options are B, KB, MB, GB and TB.

.PARAMETER DecimalPlaces
Round numbers to this many decimal places.

.PARAMETER CommaSeperated
Add commas to the numbers for readability.

.EXAMPLE
Get-DriveInfo
Gets drive info for all drive letters

.EXAMPLE
Get-DriveInfo -MeasureUnit GB -DecimalPlaces 2 -CommaSeperated
Gets drive info for all drive letters.  Show sizes in GB with up to 2 decimal places and seperate thousands with commas.

.EXAMPLE
Get-DriveInfo C
Gets info for the C drive

.EXAMPLE
Get-DriveInfo C, D | Format-Table -Auto
Gets drive info for the C and D drives.  The output is formatted into a table.

.EXAMPLE
Get-DriveInfo | Out-GridView
Gets drive info for for all drives and displays it in a GridView window.
#>

  \[CmdletBinding()\]
  Param (
    \[Parameter(ValueFromPipeline=$true)\]
    \[string\[\]\]$DriveLetter = "\*",
   
    \[ValidateSet("B", "KB", "MB", "GB", "TB")\]
    \[string\]$MeasureUnit = "B",
   
    \[int\]$DecimalPlaces = 2,
   
    \[alias("HumanReadable")\]
    \[alias("H")\]
    \[switch\]$CommaSeperated
  )
 
  Process {
    $DivideBy = "1" + $MeasureUnit
    If ( $MeasureUnit -eq "B" ) { $DivideBy = 1 }
    $NumberFormat = ("0." + ("#" \* $DecimalPlaces)).Trim()
    If ($CommaSeperated) { $NumberFormat = "#,##" + $Numberformat }
   
    If ($DriveLetter -eq "\*") {
      $DriveLetter = Get-PSDrive -PSProvider "FileSystem" | Where-Object {$\_.Free -and $\_.Used} | Select-Object -ExpandProperty Name
    }
   
    ForEach ($thisDrive in $DriveLetter) {
      If ($thisDrive.Length -gt 1) {$thisDrive = $thisDrive.Substring(0,1)}
      $thisPSDrive = Get-PSDrive -Name $thisDrive
      $psdTotal = $thisPSDrive.Free + $thisPSDrive.Used
      $psdUsed = ($thisPSDrive.Used/$psdTotal)\*100
      $psdFree = ($thisPSDrive.Free/$psdTotal)\*100
      $props = \[Ordered\]@{
        Drive = $thisPSDrive.Root
        Root = $thisPSDrive.DisplayRoot
        Free = $thisPSDrive.Free / $DivideBy
        Used = $thisPSDrive.Used / $DivideBy
        Total = $psdTotal / $DivideBy
        FreePercent = $psdFree
        UsedPercent = $psdUsed
      }
      
      If (-not $props.Root) {
        $props.Root = $props.Drive
      }
     
      If ( $NumberFormat -ne "0.") {
        $props.Total = $props.Total.ToString($NumberFormat)
        $props.Used = $props.Used.ToString($NumberFormat)
        $props.Free = $props.Free.ToString($NumberFormat)
        $props.FreePercent = $props.FreePercent.ToString($NumberFormat)
        $props.UsedPercent = $props.UsedPercent.ToString($NumberFormat)
      }
     
      New-Object PSObject -Property $props
    }
  }
}
