---
title: "Execute VB.Net or C# Code"
date: "2017-09-02"
categories: 
  - "powershell"
tags: 
  - "powershell"
---

Add-Type -Language VisualBasic @"
public class IPv4Address
    public octet(3) as integer	
	public overrides function ToString() as string
		return octet(0) & "." & octet(1) & "." & octet(2) & "." & octet(3)
	end function
end class
"@
 
$addr = New-Object IPv4Address
