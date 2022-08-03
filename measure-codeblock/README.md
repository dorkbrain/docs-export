---
title: "Measure-CodeBlock"
date: "2017-09-02"
categories: 
  - "powershell"
tags: 
  - "powershell"
---

Function Measure-CodeBlock (\[scriptblock\]$ScriptBlock) {
	$StopWatch = New-Object System.Diagnostics.StopWatch
	$StopWatch.Start()
	$ScriptBlock.Invoke()
	$StopWatch.Stop()
	$StopWatch.ElapsedMilliseconds
}
