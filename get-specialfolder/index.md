---
title: "Get-SpecialFolder"
date: "2017-09-02"
categories: 
  - "powershell"
tags: 
  - "powershell"
---

Function Get-SpecialFolder {
<#
  .SYNOPSIS
  Gets a list of special/shell folder names and their paths
  .DESCRIPTION
  Get-SpecialFolder gets a list of special/shell folder names and the paths associated with them.
 
  These can be used to easily find directories such as the logged in users desktop, the common start menu, or the users favorites.
  .EXAMPLE
  Get-SpecialFolder
  This will return the names and paths of all configured special folders on this system
  .EXAMPLE
  Get-SpecialFolder -Name MyDocuments
  This will return the full path to Documents folder of the logged in user
  .EXAMPLE
  Get-SpecialFolder -Name Desktop, ProgramFiles
  This will return the paths to the Desktop and Program Files folders
  .EXAMPLE
  Get-SpecialFolder -Name Desktop | Get-Item
  This will get the path for the users desktop and pipe it to Get-Item which will return a DirectoryInfo object
  .PARAMETER Name
  The name of the special folder to resolve.  If this is omitted all special folders will be returned.
#>
 
  \[CmdletBinding()\]
  Param (
    \[Parameter(Position=0,Mandatory=$false,ValueFromPipeline=$true)\]
    \[string\[\]\]$Name
  )
 
  Begin { 
    ### Only add the custom structure once to avoid errors
    ### I'm building a custom structure because ordered hash tables aren't available in PS v2
    If (-not (\[System.Management.Automation.PSTypeName\]"SpecialFolderStruct").Type) {
      Add-Type -Language VisualBasic @"
Imports System.Runtime.InteropServices
<StructLayout(LayoutKind.Sequential)> Public Structure SpecialFolderStruct
  Public Name As String
  Public Path As String
End Structure
"@
    }
 
    ### If a name hasn't been provided then get a list of all special folders
    If (-not $Name) {
      $allFolders = @()
      ### Loop through all names in the SpecialFolder enumeration
      \[Environment+SpecialFolder\]::GetNames(\[Environment+SpecialFolder\]) | ForEach-Object {
        $thisFolder = New-Object SpecialFolderStruct
        $thisFolder.Name = $\_
        $thisFolder.Path = \[Environment\]::GetFolderPath($\_)
        ### Skip specials that don't have a path (ie: MyComputer)
        If ($thisFolder.Path) {  $allFolders += $thisFolder }
      }
    }
  }
 
  Process { 
    ### If a name has been provided then search for a single special folder
    If ($Name) {
      $Name | ForEach-Object {
        $thisName = $\_
        $thisItem = New-Object SpecialFolderStruct
        Try {
          ### Look for the special folder using the name provided
          $thisItem.Name = \[Environment+SpecialFolder\]::$thisName.ToString()
          $thisItem.Path = \[Environment\]::GetFolderPath($thisName)
        } Catch {
          ### Caught an error - Probably searched for a bad folder name
          $thisItem.Name = $thisName
          $thisItem.Path = $null
        } Finally {
          ### Return the results to the pipeline
          $thisItem
        }
      }
    }
  }
 
  End { 
    ### If a name hasn't been provided then return all of the folders to the pipeline
    If (-not $Name) { $allFolders }
  }
}
