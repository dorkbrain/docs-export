---
title: "Predictable Network Interface Names"
date: "2017-08-27"
categories: 
  - "linux"
tags: 
  - "linux"
---

In newer versions of linux using systemd/udev v197 and higher (ie: CentOS/RHEL 7) the interface naming scheme has been changed to something that describes where the physical adapter is located in the computer.

| Interface Types | - |
| --- | - |
| en | Ethernet |
| wl | Wireless LAN (WLAN) |
| wn | Wireless Wide Area Network (WWAN) |

 

| Location Formats | - |
| --- | - |
| o<index> | On-board device |
| s<slot>\[f<function>\]\[d<dev\_id>\] | Hotplug |
| x<mac> | MAC Address |
| p<bus>s<slot>\[f<function>\]\[d<dev\_id>\] | PCI geographical location |
| p<bus>s<slot>\[u<port>\]\[..\]\[c<config>}\[i<interface>\] | USB |

For example: ens32 = Ethernet Hotplug Slot 32
