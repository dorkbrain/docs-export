---
title: "Generate a Timestamp in a Batch Script"
date: "2017-08-26"
categories: 
  - "windows"
tags: 
  - "windows"
---
```
rem Replace empty spaces with zero
set now=%time: =0%

set hour=%now:~0,2%
set minute=%now:~3,2%
set second=%now:~6,2%

set year=%date:~-4%
set month=%date:~4,2%
set day=%date:~7,2%

rem Example: 20160907\_081153
set timestamp=%year%%month%%day%\_%hour%%minute%%second%
```
