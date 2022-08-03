---
title: "Get AD Group Membership"
date: "2017-09-02"
categories: 
  - "powershell"
tags: 
  - "powershell"
---

This script will work without having to import the ActiveDirectory module

$uid = Read-Host "Enter a user ID \[$($env:username)\]"
if ($uid -eq "") { $uid = $($env:username) }

"Getting group membership for $uid . . ."
$groupList = (\[DirectoryServices.DirectorySearcher\]("(&(objectCategory=User)(samAccountName=$uid))")).FindOne().GetDirectoryEntry().memberOf | Sort-Object
$groupOutput = @()
$groupList | ForEach-Object {
  $newObject=\[PSCustomObject\]\[Ordered\]@{ Group = ""; Path = ""; Domain = ""; FullDN = $\_ }

  $newObject.FullDN.Split(",") | ForEach-Object {
    $thisItem = $\_.Split("=")
    Switch ($thisItem\[0\]) {
      "CN" { $newObject.Group = $thisItem\[1\] }
      
      "OU" {
        If (-not $newObject.Path) {
          $newObject.Path = $thisItem\[1\]
        } Else {
          $newObject.Path = $thisItem\[1\] + "\\"  + $newObject.Path
        }
      }
      
      "DC" {
        If (-not $newObject.Domain) {
          $newObject.Domain = $thisItem\[1\]
        } Else {
          $newObject.Domain += "." + $thisItem\[1\]
        }
      }
    }
  }

  $groupOutput += $newObject
}
"Displaying group list in a grid view . . ."
$groupOutput | Out-GridView -Title "Group Membership for $uid)" -Wait
"Script complete!"
