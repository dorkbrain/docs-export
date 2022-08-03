---
title: "Trimming Whitespace in BASH"
date: "2017-09-23"
categories: 
  - "linux"
tags: 
  - "linux"
---

This function will trim leading and trailing tab and space characters from a string:

trim() {
  printf "$\*" | awk '{ gsub(/^\[ \\t\]+|\[ \\t\]+$/, ""); print }'
}
