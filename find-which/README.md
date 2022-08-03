---
title: "Find-Which"
date: "2017-09-02"
categories: 
  - "powershell"
tags: 
  - "powershell"
---

Similar to the Linux which command. Searches the system path for executables matching the name provided. Included at the bottom is a sample which.cmd script to make it easier to use at a DOS prompt.

function Find-Which {
  param(
    $ExecName
  )
  
  Begin {
    $Path = (".;"+$ENV:PATH).Split(";")
    for ($i=0; $i -le ($Path.Length - 1); $i++) {
      If (-not $Path\[$i\].EndsWith("\\") ) {
        $Path\[$i\] = $Path\[$i\] + "\\"
      }
      $Path\[$i\] = \[System.Environment\]::ExpandEnvironmentVariables($Path\[$i\])
    }
    
    $PathExt = ($ENV:PATHEXT).Split(";")
    for ($i=0; $i -le ($PathExt.Length - 1); $i++) {
      $PathExt\[$i\] = $PathExt\[$i\].ToLower()
    }
    
    \[string\[\]\]$builtIn = @("ASSOC", "CALL", "CD", "CHDIR", "CLS", "COLOR", 
      "COPY", "DATE", "DEL", "DIR", "ECHO", "ENDLOCAL", "ERASE", "EXIT", "FOR", 
      "FTYPE", "GOTO", "IF", "MD", "MKDIR", "MKLINK", "MOVE", "PATH", "PAUSE", 
      "POPD", "PROMPT", "PUSHD", "RD", "REM", "REN", "RMDIR", "SET", "SETLOCAL", 
      "SHIFT", "START", "TIME", "TITLE", "TYPE", "VER", "VERIFY", "VOL")
  }

  Process {
    if ( $builtIn -contains $ExecName ) {
      "{0} is built into cmd.exe" -f $ExecName.ToUpper()
      Find-Which -ExecName "cmd.exe"
    }
    ForEach ($thisPath in $Path) {
      $testFile = Join-Path $thisPath $ExecName
      $found = Get-ChildItem -Path $testFile -ErrorAction SilentlyContinue
      if ($found) { 
        $found.FullName
      }
      ForEach ($thisExt in $PathExt) {
      $testFile = (Join-Path $thisPath $ExecName) + $thisExt
        $found = Get-ChildItem -Path $testFile -ErrorAction SilentlyContinue
        if ($found) { 
          $found.FullName
        }    
      }
    }
  }
}

""

ForEach ($arg in $args) {
  Find-Which $arg
}

""

 

**which.cmd**

@echo off
powershell -nologo -noprofile -f c:\\myfiles\\ps1\\find-which.ps1 %\*
