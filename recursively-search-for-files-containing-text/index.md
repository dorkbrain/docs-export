---
title: "Recursively search for files containing text"
date: "2017-08-27"
categories: 
  - "linux"
tags: 
  - "linux"
---

find /etc -type f -print0 | xargs -0 grep -i 'some text'
