---
title: "Adding a Trusted Root Certificate in Ubuntu"
date: "2017-08-27"
categories: 
  - "linux"
tags: 
  - "linux"
---

1. sudo mkdir /usr/share/ca-certificates/extra
2. Copy your CA .crt file to the new directory
3. sudo dpkg-reconfigure ca-certificates
4. Follow the on-screen prompts
