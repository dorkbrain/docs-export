---
title: "Speed Up Execution By Disabling CRL Checks"
date: "2017-09-03"
categories: 
  - "powershell"
tags: 
  - "powershell"
---

By default PowerShell checks the **C**ertificate **R**evocation **L**ist for all digitally signed modules or code. This can significantly increase the time it takes to load an assembly or module. There are a few ways to do this including disabling CRL checks in Internet Explorer's security settings. This will work but it will also disable CRL checks for SSL enabled web sites which can be a big security concern. The below method will disable the checks just for the PowerShell executable.

You'll need to edit or create a file called \*\*powershelle.exe.config\*\* in a text editor: \* 32-bit PowerShell: C:\\Windows\\System32\\WindowsPowerShell\\v1.0 \* 64-bit PowerShell: C:\\Windows\\syswow64\\WindowsPowerShell\\v1.0

Add the below XML:

<configuration>
  <runtime>
    <generatePublisherEvidence enabled="false"/>
  </runtime>
</configuration>
