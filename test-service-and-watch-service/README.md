---
title: "Test-Service and Watch-Service"
date: "2017-09-02"
categories: 
  - "powershell"
tags: 
  - "powershell"
---

Function Test-Service ($Server = ".", $Service) {
    $services = Get-Service -ComputerName $Server | Where-Object {($\_.Name -like $Service) -or ($\_.DisplayName -like $Service)}
    ForEach($thisService in $Services) {
        \[PSCustomObject\]$newObject = \[PSCustomObject\]\[Ordered\]@{
            Date = (Get-Date -Format "MM/dd/yyyy").Trim()
            Time = (Get-Date -Format "hh:mm:ss tt").Trim()
            Host = $Server.Trim()
            Status = $thisService.Status.ToString().Trim()
            Service = $thisService.Name.Trim()
            DisplayName = $thisService.DisplayName.Trim()
        }
        \[string\[\]\]$DefaultProperties = 'Time', 'Host', 'Status', 'Service'
        If ($Server.Trim() -eq ".") { \[string\[\]\]$DefaultProperties = 'Date', 'Time', 'Status', 'Service' }
        $ddps = New-Object System.Management.Automation.PSPropertySet DefaultDisplayPropertySet,$DefaultProperties
        $PSStandardMembers = \[System.Management.Automation.PSMemberInfo\[\]\]$ddps
        $newObject | Add-Member -MemberType MemberSet -Name PSStandardMembers -Value $PSStandardMembers -PassThru
    }
}
 
Function Watch-Service ($Server = ".", $Service, $Interval = 5) {
    While($true) {
        Test-Service -Host $Server -Service $Service 
        Start-Sleep $Interval
    }
}
