---
title: "Get init System in CentOS"
date: "2017-08-27"
categories: 
  - "linux"
tags: 
  - "linux"
---

This one liner is useful to determine the init system used on RPM based distros.

rpm -qf $(file /proc/1/exe | awk 'match($0, /.\*\`(.\*)'\\''/, ary){print ary\[1\]}')
