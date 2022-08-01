---
title: "Bash - Login and Logoff Script Order"
date: "2017-08-27"
categories: 
  - "linux"
tags: 
  - "linux"
---

| Script | Context |
| --- | --- |
| /etc/rc.d/rc.local | All Users @boot |
| /etc/profile | All Users @login |
| /etc/profile.d/\*.sh | All Users @login |
| ~/.bash\_profile | Single User @login |
| ~/.bashrc | Single User @login |
| ~/.bash\_logoff | Single User @logoff |
