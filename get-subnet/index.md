---
title: "Get-Subnet"
date: "2017-09-02"
categories: 
  - "powershell"
tags: 
  - "powershell"
---

Function Get-Subnet {
<#
.DESCRIPTION
Displays info about an address and subnet.

.PARAMETER IpAddress
AKA: -IP or -Address
This can be a simple IP address or an address in CIDR notation (ie: 1.2.3.4/5).

.PARAMETER MaskBits
AKA: -CIDR
Number of mask bits (CIDR bits).

.PARAMETER SubnetMask
AKA: -Subnet
Standard subnet mask.

.EXAMPLE
Get-Subnet -IpAddress 192.168.1.20/24
Use CIDR notation to get subnet info.

.EXAMPLE
192.168.1.20/24 | Get-Subnet
Same as above.

.EXAMPLE
Get-Subnet -IpAddress 192.168.1.20 -MaskBits 24
Same as above.

.EXAMPLE
Get-Subnet -IpAddress 192.168.1.20 -SubnetMask 255.255.255.0
Same as above but using as subnet mask instead of mask bits.
#>

  \[CmdletBinding(DefaultParameterSetName="Bits")\]
  Param (
    \[Alias("IP","Address")\]
    \[Parameter(ParameterSetName="Subnet", Mandatory = $true, ValueFromPipeline=$true, Position = 0)\]
    \[Parameter(ParameterSetName="Bits",   Mandatory = $true, ValueFromPipeline=$true, Position = 0)\]
    \[String\]$IpAddress,
    
    \[Alias("Subnet")\]
    \[Parameter(ParameterSetName="Subnet", Mandatory = $true, Position = 1)\]   
    \[IpAddress\]$SubnetMask,
    
    \[Alias("CIDR")\]
    \[Parameter(ParameterSetName="Bits",   Mandatory = $false, Position = 1)\]   
    \[Int\]$MaskBits = -1
  )
  
# For v2 compatibility I'm creating an ordered structure in VB.Net
Add-Type -Language VisualBasic @"
Imports System.Runtime.InteropServices
<StructLayout(LayoutKind.Sequential)> Public Structure StructAddressInfo
    Public IpAddress As System.Net.IpAddress
    Public IpBinary As String
    Public SubnetMask As System.Net.IpAddress
    Public SubnetBinary As String
    Public MaskBits As Integer
    Public HostBits As Integer
    Public NetworkAddress As System.Net.IpAddress
    Public NetworkAddressBinary As String
    Public BroadcastAddress As System.Net.IpAddress
    Public BroadcastAddressBinary As String
    Public AddressesPerNetwork As Integer
End Structure
"@  

  # If slash notation was used then split the address and mask bits
  If ( ($PsCmdlet.ParameterSetName -eq "Bits") -and ($MaskBits -eq -1) ) {
    If ( $IpAddress.Contains("/") ) {
      $MaskBits = $IpAddress.Split("/")\[1\]
      $IpAddress = $IpAddress.Split("/")\[0\]
    } Else {
      Throw "Without -MaskBits or -SubnetMask I'm expecting -IpAddress in CIDR notation."
    }
  }
  
  # Convert the address into an \[IpAddress\] type instead of a string
  \[IpAddress\]$IpAddress = \[IpAddress\]::Parse($IpAddress)
  
  # Convert the address into a binary string
  $IpBinaryArray = $("0","0","0","0")
  0..3 | ForEach-Object { 
    $IpBinaryArray\[$\_\] = \[Convert\]::ToString($IpAddress.GetAddressBytes()\[$\_\], 2).PadLeft(8, "0") 
  }
  $IpBinary = $IpBinaryArray -Join ""
  
  
  # Which parameter set are we using?
  Switch ($PsCmdlet.ParameterSetName) {
    "Bits" {
      # Generate binary string based on MaskBits
      $SubnetBinary = ("1" \* $MaskBits).PadRight(32,"0")
      # Convert the binary string into a dotted decimal address
      \[IpAddress\]$SubnetMask = "{0}.{1}.{2}.{3}" -f \[Convert\]::ToInt32($SubnetBinary.Substring(0, 8), 2),
        \[Convert\]::ToInt32($SubnetBinary.Substring(8, 8), 2),
        \[Convert\]::ToInt32($SubnetBinary.Substring(16, 8), 2),
        \[Convert\]::ToInt32($SubnetBinary.Substring(24, 8), 2)
    }
    
    "Subnet" {
      # Generate binary string based on SubnetMask
      $SubnetBinaryArray = $("0","0","0","0")
      0..3 | ForEach-Object { 
        $SubnetBinaryArray\[$\_\] = \[Convert\]::ToString($SubnetMask.GetAddressBytes()\[$\_\], 2).PadLeft(8, "0") 
      }
      $SubnetBinary = $SubnetBinaryArray -Join ""
      # Calculate MaskBits based on the binary subnet mask string
      $MaskBits = ($SubnetBinary -Join "").LastIndexOf("1") + 1
    }
  }
  
  # Calculate binary network address based on mask bits
  $NetworkAddressBinary = $IpBinary.Substring(0, $MaskBits).PadRight(32, "0")
  # Convert binary string to dotted decimal address
  \[IpAddress\]$NetworkAddress = "{0}.{1}.{2}.{3}" -f \[Convert\]::ToInt32($NetworkAddressBinary.Substring(0, 8), 2),
    \[Convert\]::ToInt32($NetworkAddressBinary.Substring(8, 8), 2),
    \[Convert\]::ToInt32($NetworkAddressBinary.Substring(16, 8), 2),
    \[Convert\]::ToInt32($NetworkAddressBinary.Substring(24, 8), 2)

  $BroadcastAddressBinary = $IpBinary.Substring(0, $MaskBits).PadRight(32, "1")
  # Calculate binary broadcast address based on mask bits
  \[IpAddress\]$BroadcastAddress = "{0}.{1}.{2}.{3}" -f \[Convert\]::ToInt32($BroadcastAddressBinary.Substring(0, 8), 2),
  # Convert binary string to dotted decimal address
    \[Convert\]::ToInt32($BroadcastAddressBinary.Substring(8, 8), 2),
    \[Convert\]::ToInt32($BroadcastAddressBinary.Substring(16, 8), 2),
    \[Convert\]::ToInt32($BroadcastAddressBinary.Substring(24, 8), 2)
     
  # Calculate IP addresses per network
  $AddressesPerNetwork = \[Convert\]::ToInt32("1"\*(32-$MaskBits), 2) + 1
  
  # Create new instance of our custom structure
  $AddressInfo = New-Object StructAddressInfo
  # Populate properties of custom structure
  $AddressInfo.IpAddress = $IpAddress
  $AddressInfo.IpBinary = $IpBinary
  $AddressInfo.SubnetMask = $SubnetMask
  $AddressInfo.SubnetBinary = $SubnetBinary
  $AddressInfo.MaskBits = $MaskBits  
  $AddressInfo.HostBits = 32 - $MaskBits  
  $AddressInfo.NetworkAddress = $NetworkAddress
  $AddressInfo.NetworkAddressBinary = $NetworkAddressBinary
  $AddressInfo.BroadcastAddress = $BroadcastAddress
  $AddressInfo.BroadcastAddressBinary = $BroadcastAddressBinary
  $AddressInfo.AddressesPerNetwork = $AddressesPerNetwork
  # Return the custom structure and exit
  Return $AddressInfo
}
