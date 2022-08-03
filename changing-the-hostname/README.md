---
title: "Changing the Hostname"
date: "2017-08-28"
categories: 
  - "linux"
tags: 
  - "linux"
---

For most modern distros:

hostname <newhostname>
hostnamectl set-hostname $(hostname)

 

 

CentOS 6: - Edit /etc/hosts - Edit /etc/sysconfig/network - Run hostname <newhostname>
