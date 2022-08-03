---
title: "Get-ScriptPath"
date: "2017-09-02"
categories: 
  - "powershell"
tags: 
  - "powershell"
---

Function Get-ScriptPath {
	If ($PSVersionTable.PSVersion.Major -lt 3) {
		# We're in version 1 or 2, do it the hard way
		Split-Path -Parent $MyInvocation.MyCommand.Definition
	} Else {
		# We're in version 3 or higher, do it the easy way
		$PSScriptRoot
	}
}
