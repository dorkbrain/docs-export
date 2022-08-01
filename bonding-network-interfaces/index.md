---
title: "Bonding Network Interfaces"
date: "2017-08-28"
categories: 
  - "linux"
tags: 
  - "linux"
---

1) Configure the bonding kernel module to load on startup. This step is unnecessary if your version of initscripts is newer than September 2003 Check the version with 'rpm -qi initscripts' or 'dpkg -s initscripts' 

vi /etc/sysconfig/modules/bonding.modules

#!/bin/bash
if \[ ! -d /sys/module/bonding \] ; then
/sbin/modprobe bonding >/dev/null 2>&1
fi

chmod +x /etc/sysconfig/modules/bonding.modules

 

2) Configure the bond0 interface.

vi /etc/sysconfig/network-scripts/ifcfg-bond0

DEVICE=bond0
IPADDR=x.x.x.x
NETMASK=x.x.x.x
GATEWAY=x.x.x.x
ONBOOT=yes
BOOTPROTO=none
USERCTL=no
NM\_CONTROLLED=no
BONDING\_OPTS="mode=balance-alb miimon=100"

For detailed BONDING\_OPTS configuration options see [Specific Kernel Module Capabilities](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/6/html/Deployment_Guide/sec-Specific_Kernel_Module_Capabilities.html#sec-Using_Channel_Bonding) GATEWAY=x.x.x.x can be replaced with PREFIX=x if you wish to use CIDR notation

 

3) Configure the physical interfaces. Replace any existing file contents.

vi /etc/sysconfig/network-scripts/ifcfg-eth0
DEVICE=eth0
BOOTPROTO=none
ONBOOT=yes
MASTER=bond0
SLAVE=yes
USERCTL=no
NM\_CONTROLLED=no

vi /etc/sysconfig/network-scripts/ifcfg-eth1
DEVICE=eth1
BOOTPROTO=none
ONBOOT=yes
MASTER=bond0
SLAVE=yes
USERCTL=no
NM\_CONTROLLED=no

 

5) Restart networking and check bond0.

service network restart
ifconfig bond0
cat /sys/class/net/bonding\_masters
cat /proc/net/bonding/bond0

 

6) Check the status of bond0.

cat /proc/net/bonding/bond0

 

**Sample LACP Setup** The example below uses the same config as above except for the BONDING\_OPTS.

BONDING\_OPTS="mode=802.3ad miimon=100"

 

Example Cisco switch config:

int Port-channel1
description Port channel for linux
switchport mode access
no shut
exit

int range Gi0/1-2
description LACP Channel 1
channel-protocol lacp
channel-group 1 mode active
no shut
exit

See [Cisco LACP Command Reference](http://www.cisco.com/c/en/us/td/docs/optical/cpt/r9_3/command/reference/cpt93_cr/cpt93_cr_chapter_01000.html) for more info on switch config.
