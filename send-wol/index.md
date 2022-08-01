---
title: "Send-WOL"
date: "2017-09-02"
categories: 
  - "powershell"
tags: 
  - "powershell"
---

Function Send-WOL( \[string\]$HWAddress, \[string\]$IPAddress="0.0.0.0", \[int\]$Port=7 ) {
  # Get rid of non-alphanumeric chars
  $HWAddress = $HWAddress -Replace "\[^a-zA-Z0-9\]", "" 
  
  # Convert HWAddress to a byte array
  \[byte\[\]\]$macAddress = ($HWAddress -Split "(..)" | Where-Object { -Not \[string\]::IsNullOrEmpty($\_) } | ForEach-Object { \[Byte\]("0x" + $\_) })

  # Construct magic packet (always 102 bytes, 6 FF's followed by the MAC 16 times)
  \[byte\[\]\]$magicPacket = \[byte\[\]\](,0xff\*6) + $macAddress\*16
  
  # Create UDP client
  $udpClient = New-Object System.Net.Sockets.Udpclient

  # Configure UDP client to send to the broadcast address on port 7
  $udpClient.Connect((\[System.Net.IPAddress\]::Broadcast), $Port)

  # Send the WOL magic packet
  $udpClient.Send($magicPacket, $packet.Length) | Out-Null
  
  # If an IP was specified loop until it's responding to pings
  If ( $IPAddress -ne "0.0.0.0" ) {
    Do {
      "Waiting for {0} to come up..." -f $IPAddress
    } Until ( (Test-Connection $IPAddress -Count 1 -ErrorAction SilentlyContinue).StatusCode -eq 0 )
    "{0} is now responding." -f $IPAddress
  }
}

# -IPAddress can be either a computer name or a dotted decimal address.
# If -IPAddress is omitted then a WOL packet will be sent and the function
#  won't wait for a response from the target.

Send-WOL -HWAddress "00-25-22-BE-61-B0" -IPAddress 192.168.1.10
