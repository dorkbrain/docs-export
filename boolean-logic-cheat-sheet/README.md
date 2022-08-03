---
title: "Boolean Logic Cheat Sheet"
date: "2017-08-27"
categories: 
  - "misc"
tags: 
  - "misc"
---

| **x** | **y** |   | **x and y** | **x nand y** | **x or y** | **x nor y** | **x xor y** | **not x** |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| false | false |  | false | **true** | false | **true** | false | **true** |
| **true** | false |  | false | **true** | **true** | false | **true** | false |
| false | **true** |  | false | **true** | **true** | false | **true** |  |
| **true** | **true** |  | **true** | flase | **true** | false | false |  |

 

| Evaluates to **true** if: |
| --- |
| and | If both x and y are **true** |
| nand | As long as both x and y aren't **true** |
| or | If either x or y or both are **true** |
| nor | If neither x or y are **true** |
| xor | If either x or y but not both are **true** |
| not | If value is false |
