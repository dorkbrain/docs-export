---
title: "Building a custom VMware ESXi ISO"
date: "2017-09-02"
categories: 
  - "powershell"
tags: 
  - "powershell"
---

For this demo I'll be injecting the latest HP Proliant VIB's from http://vibsdepot.hp.com/hpq/latest/esxi-600-bundles into ESXi 6 and building a custom install ISO.

Folder layout: D:\\ESXi Custom Image - This is where I put my ps1 file and where the ISO will be created D:\\ESXi Custom Image\\depots - This is where the ZIP files for the VIB's are stored

1) You will need the ESXi Offline Bundle ZIP file (stored in the depots folder) and you will need to download and install the latest PowerCLI. Both can be found on https://www.vmware.com

2) Download the ZIP files from http://vibsdepot.hp.com/hpq/latest/esxi-600-bundles to the depots folder as well. Only using the HP site as an example. You can place ZIP's from other vendors here as well. The script will add anything that's not from VMware automatically.

3) Save the below text as a .ps1 file. Change the $root, $depots, $profileName, and $vendorName variables to your liking then run the script.

$root = "D:\\ESXi Custom Image"
$depots = Join-Path $root "depots"
$profileName = "Custom"
$vendorName = "DorkBrain"

""
"Adding VMware ImageBuilder Snapin from PowerCLI"
Add-PSSnapin VMware.ImageBuilder

""
"Adding $depots"
Get-ChildItem (Join-Path $depots "\*.zip") | %{ 
  "  " + $\_.name
  Add-EsxSoftwareDepot $\_.fullname | Out-Null
}

""
"Creating new profile $profileName"
$sourceProfile = (Get-EsxImageProfile | Where-Object {$\_.Name -match "ESXi.\*\[0-9\]-standard"}).Name
New-EsxImageProfile -CloneProfile $sourceProfile -Name $profileName -Vendor $vendorName | Out-Null

""
"Adding extra VIB's"
Get-EsxSoftwarePackage | Where-Object { $\_.Vendor -ne "VMware" } | %{ 
  "  " + $\_.Name
  Add-EsxSoftwarePackage -ImageProfile $profileName -SoftwarePackage $\_.Name | Out-Null
}

$isoName = (Join-Path $root ($vendorName + "-" + $profileName + "-" + ((Get-EsxImageProfile -Name $profileName).VibList | Where-Object { $\_.Name -eq "esx-base" } | Select-Object -ExpandProperty Version) + ".iso"))

""
"Creating $isoName"
Export-EsxImageProfile -ImageProfile $profileName -ExportToIso -FilePath $isoName -Force
""
"Done!"

That's it! You now have a custom ESXi install ISO. Burn it to a CD or put it on a USB drive with Rufus found [here](https://rufus.akeo.ie/). Make sure you let it update the menu.c32 file or the boot process will hang.
