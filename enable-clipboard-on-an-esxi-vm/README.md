---
title: "Enable clipboard on an ESXi VM"
date: "2017-08-26"
categories: 
  - "vmware"
tags: 
  - "vmware"
---

This allows you to copy and paste text to/from your client and the guest VM. This is disabled by default and has to be done on each virtual machine.

Add the following two entries to the advanced VM config while the guest is powered off:

| **Key** | **Value** |
| --- | --- |
| isolation.tools.copy.disable | FALSE |
| isolation.tools.paste.disable | FALSE |
