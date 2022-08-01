---
title: "Enabling SNMP on ESXi 6"
date: "2017-08-26"
categories: 
  - "vmware"
tags: 
  - "vmware"
---

Simple set of commands to enable SNMP on ESXi 6.x

esxcli system snmp set -r
esxcli system snmp set -c public
esxcli system snmp set -p 161
esxcli system snmp set -L "My Location"
esxcli system snmp set -C admin@email.com
esxcli system snmp set -e yes
