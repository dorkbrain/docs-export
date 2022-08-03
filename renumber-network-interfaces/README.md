---
title: "Renumber Network Interfaces"
date: "2017-08-27"
categories: 
  - "linux"
tags: 
  - "linux"
---

- Stop networking with 'service network stop'
- Comment or remove necessary SUBSYSTEM lines from /etc/udev/rules.d/70-persistent-net.rules
- Remove necessary ifcfg-eth# files from /etc/sysconfig/network-scripts
- Start networking with 'service network start'
