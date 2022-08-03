---
title: "Map-Drive and Unmap-Drive"
date: "2017-09-02"
categories: 
  - "powershell"
tags: 
  - "powershell"
---

Function Map-Drive{
    param(\[string\]$DriveLetter,
          \[string\]$NetworkPath)
 
  # Get a list of currently mapped network drives
  $networkDrives = get-wmiobject -class "Win32\_MappedLogicalDisk" -namespace "root\\CIMV2"
 
  # Check to see if the drive already exists
  If (Get-PSDrive | Where-Object {$\_.Name -eq $DriveLetter.Substring(0,1)}) {
    Try { 
      # Letter already exists, is it local or network?
      $oldDrive = $networkDrives | Where-Object { $\_.Name -eq $DriveLetter }
      if ( $oldDrive ) { # It's a network drive
        If ( $oldDrive.ProviderName -ne $NetworkPath ) {
          # And it's mapped to a different location, better unmap it
          $(New-Object -com WScript.Network).RemoveNetworkDrive($DriveLetter)
          # Delay two seconds before trying to map to the same drive letter
          #   to give the OS a chance to disconnect from the old server first
          Start-Sleep -Second 2
        } Else {
          # This drive is already mapped to where we want it, don't do anything
          Return $false
        }
      } Else {
        # It's a local drive and not going anywhere
        Return $false
      }
    } Catch { 
      # Couldn't unmap, probably not a network mapping
    }
  }
  Try { 
    # Map the drive
    $(New-Object -com WScript.Network).MapNetworkDrive($DriveLetter, $NetworkPath) 
    Return $true
  } Catch { 
    Return $false
  }
}
 
Function Unmap-Drive ($DriveLetter) {
    If ( $DriveLetter.Length -eq 1 ) { $DriveLetter = $DriveLetter + ":\\" }
    If ( $DriveLetter.Length -eq 2 ) { $DriveLetter = $DriveLetter + "\\" }
    $vol = get-wmiobject -Class Win32\_Volume | where{$\_.Name -eq "${DriveLetter}:\\"}  
    $vol.DriveLetter = $null  
    $vol.Put()  
    $vol.Dismount($false, $false)
}
