---
title: "STDIO Redirection"
date: "2017-09-03"
categories: 
  - "powershell"
tags: 
  - "powershell"
---

\# The ProcessStartInfo object stores the configuration for the process
# we want to run.
$pinfo = New-Object System.Diagnostics.ProcessStartInfo
 
# This is the executable.
$pinfo.FileName = "cmd.exe"
 
# These are the arguments we need to pass to the executable.
$pinfo.Arguments = @("/c", "dir", "/s", '"c:\\\*.\*"')
 
# Don't use Shell Execute.  This is required to redirect stdout and stderr.
$pinfo.UseShellExecute = $false
 
# Don't spawn a new window for this process.
$pinfo.CreateNoWindow = $true
 
# Redirect stderr and stdout.
$pinfo.RedirectStandardError = $true
$pinfo.RedirectStandardOutput = $true
 
# Start the process.
$myproc = New-Object System.Diagnostics.Process
$myproc.StartInfo = $pinfo
$myproc.Start() | Out-Null
 
# If we do this after WaitForExit and either buffer fills to max capacity 
# the process will never end.  It will just pause until the buffer is dumped 
# to make more room.
$stdout = $myproc.StandardOutput.ReadToEnd()
$stderr = $myproc.StandardError.ReadToEnd()
 
# Wait for the process to end before continuing.
$myproc.WaitForExit()
 
# If we have anything in either stderr or stdout buffer display it.
If ($stdout) { Write-Host $stdout }
If ($stderr) { Write-Error $stderr }
