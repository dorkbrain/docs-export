---
title: "Add an Assembly Only Once"
date: "2017-09-02"
categories: 
  - "powershell"
tags: 
  - "powershell"
---

This can be useful at the beginning of a script or function. If you try to add an assembly that's already loaded you might get an error so I came up with this to only load an assembly if it's not already loaded. I typically use this in the Begin{} clause in a function.

If (-Not (\[AppDomain\]::CurrentDomain.GetAssemblies() | Where-Object {$\_.FullName -Like "Microsoft.VisualBasic\*"}) ) {
  \[void\]{
    Add-Type -AssemblyName Microsoft.VisualBasic
  }
}
