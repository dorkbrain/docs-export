---
title: "Alternative to Set-Content"
date: "2017-09-02"
categories: 
  - "powershell"
tags: 
  - "powershell"
---

The Set-Content  cmdlet is wonderful but it ALWAYS adds an extra <CR><LF> line to the end of your file. If you need a file without the extra characters use \[IO.File\]::WriteAllText(filename, contents)  instead.

\[IO.File\]::WriteAllText("c:\\tmp\\test.txt", (get-childitem c:\\ | Out-String))

There is also a \[IO.File\]::AppendAllText(filename, contents)  method.
