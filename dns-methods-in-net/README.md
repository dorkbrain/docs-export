---
title: "DNS Methods in .NET"
date: "2017-09-02"
categories: 
  - "powershell"
tags: 
  - "powershell"
---

Examples using some common methods in the System.Net.Dns namespace in the .NET Framework.

| **Method** | **Parameter Object Type** | **Returns** | **Info** |
| :-- | :-- | :-- | :-- |
| GetHostAddresses | String | Array of IPAddress objects | Passing an empty string "" returns a list of IP addresses assigned to the local computer |
| GetHostEntry | String _\- or -_ IPAddress | IPHostEntry object | Passing an IP address does a reverse lookup. Another lookup is done on the resulting hostname and a list of all associated IP addresses is returned.
Passing an empty string "" returns a list of IP addresses assigned to the local computer |
| GetHostName | _none_ | String | Returns the name of the local computer |

 

**GetHostEntry Examples:**

PS C:\\> \[System.Net.DNS\]::GetHostEntry("google-public-dns-a.google.com")

HostName                                Aliases                                 AddressList
-------- ------- -----------
google-public-dns-a.google.com          {}                                      {8.8.8.8}

PS C:\\> \[System.Net.DNS\]::GetHostEntry(\[IPAddress\]::Parse("8.8.4.4"))

HostName                                Aliases                                 AddressList
-------- ------- -----------
google-public-dns-b.google.com          {}                                      {8.8.4.4}

PS C:\\> \[System.Net.DNS\]::GetHostEntry("microsoft.com")

HostName                                Aliases                                 AddressList
-------- ------- -----------
microsoft.com                           {}                                      {104.43.195.251, 23.96.52.53, 23.100...

PS C:\\Users\\dpc001> \[System.Net.DNS\]::GetHostEntry("")

HostName                                Aliases                                 AddressList
-------- ------- -----------
56Y7YW1-E6430.local.domain              {}                                      {10.20.255.76, ::1}

 

**GetHostAddresses Examples:**

PS C:\\> \[System.Net.DNS\]::GetHostAddresses("microsoft.com")

Address            : 4223871848
AddressFamily      : InterNetwork
ScopeId            :
IsIPv6Multicast    : False
IsIPv6LinkLocal    : False
IsIPv6SiteLocal    : False
IsIPv6Teredo       : False
IsIPv4MappedToIPv6 : False
IPAddressToString  : 104.43.195.251

Address            : 892624919
AddressFamily      : InterNetwork
ScopeId            :
IsIPv6Multicast    : False
IsIPv6LinkLocal    : False
IsIPv6SiteLocal    : False
IsIPv6Teredo       : False
IsIPv4MappedToIPv6 : False
IPAddressToString  : 23.96.52.53

Address            : 2944033815
AddressFamily      : InterNetwork
ScopeId            :
IsIPv6Multicast    : False
IsIPv6LinkLocal    : False
IsIPv6SiteLocal    : False
IsIPv6Teredo       : False
IsIPv4MappedToIPv6 : False
IPAddressToString  : 23.100.122.175

Address            : 601041000
AddressFamily      : InterNetwork
ScopeId            :
IsIPv6Multicast    : False
IsIPv6LinkLocal    : False
IsIPv6SiteLocal    : False
IsIPv6Teredo       : False
IsIPv4MappedToIPv6 : False
IPAddressToString  : 104.40.211.35

Address            : 3319132095
AddressFamily      : InterNetwork
ScopeId            :
IsIPv6Multicast    : False
IsIPv6LinkLocal    : False
IsIPv6SiteLocal    : False
IsIPv6Teredo       : False
IsIPv4MappedToIPv6 : False
IPAddressToString  : 191.239.213.197

PS C:\\> \[System.Net.DNS\]::GetHostAddresses("microsoft.com") | Select-Object -ExpandProperty IPAddressToString
104.43.195.251
23.96.52.53
23.100.122.175
104.40.211.35
191.239.213.197

PS C:\\> \[System.Net.DNS\]::GetHostAddresses("")

Address            : 1291785226
AddressFamily      : InterNetwork
ScopeId            :
IsIPv6Multicast    : False
IsIPv6LinkLocal    : False
IsIPv6SiteLocal    : False
IsIPv6Teredo       : False
IsIPv4MappedToIPv6 : False
IPAddressToString  : 10.20.255.76

Address            :
AddressFamily      : InterNetworkV6
ScopeId            : 0
IsIPv6Multicast    : False
IsIPv6LinkLocal    : False
IsIPv6SiteLocal    : False
IsIPv6Teredo       : False
IsIPv4MappedToIPv6 : False
IPAddressToString  : ::1

 

**GetHostName Example:**

PS C:\\> \[System.Net.DNS\]::GetHostName()
56Y7YW1-E6430
