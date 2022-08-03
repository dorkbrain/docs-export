---
title: "Magic Packet (Wake On LAN) Construction"
date: "2017-09-02"
categories: 
  - "powershell"
tags: 
  - "powershell"
---

\* The WOL magic packet is composed of 6 FF bytes followed by the MAC address 16 times for a total of 102 bytes. \* This packet is typically sent on UDP/7 or UDP/9 however the actual port does not matter. \* This packet is usually sent to the network broadcast address.

\[byte\[\]\]$magicPacket = \[byte\[\]\](,0xff\*6) + \[byte\[\]\](0x00,0x26,0xB9,0xDD,0x8A,0x18)\*16
($magicPacket | %{ $\_.ToString("X2")}) -Join " "
