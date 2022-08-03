---
title: "Creating Modules"
date: "2017-09-02"
categories: 
  - "powershell"
tags: 
  - "powershell"
---

Typically a module will consist of a group of related functions.

**Where to create your module:**

$Env:PSModulePath -Split ";" # Module search paths

**The default locations are (current user and local machine respectively):** \* $HOME\\Documents\\WindowsPowerShell\\Modules \* $PSHOME\\Modules

**To create a module:** \* Create directory inside one of the module path entries as the name of the module. \* Create a .psm1 file with the same module name \* Put your functions in the .psm1 file (Use Get-Verb to see a list of 'approved' verbs in function names)

New-ModuleManifest <path\\module\\modulename.psd1> # Fill in manifest values

**List loaded modules:**

Get-Module -ListAvailable

**List commands within a module:** Get-Command -Module <modulename>

**To auto-load a module:** $Profile | Get-Member -MemberType NoteProperty | Select-Object Name, Definition # List profile paths

**Edit/create a profile and add this line:** Get-Module -ListAvailable | Where-Object { $\_.Name -eq "<modulename>" } | Import-Module -WarningAction SilentlyContinue _\-WarningAction SilentlyContinue_ will suppress any warnings if the module uses non-standard verbs

See [Writing a Windows PowerShell Cmdlet](https://msdn.microsoft.com/en-us/library/dd878294(v=vs.85).aspx) for info on creating CmdLets using the .NET Framework
