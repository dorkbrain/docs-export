---
title: "Regular Expressions in PowerShell"
date: "2017-09-03"
categories: 
  - "powershell"
tags: 
  - "powershell"
---

We'll be using this $text variable throughout this short RegEx demo:

$text = @'
<?xml version="1.0" encoding="UTF-8"?>
<Objects>
  <Server>
    <Name>host1</Name>
    <Address>192.168.1.100</Address>
  </Server>
  <Server>
    <Name>host2</Name>
    <Address>192.168.1.101</Address>
  </Server>
</Objects>
'@

 

**\-Match**

The -Match operator will return after the first match. This returns $True if a successful match was found and $False if not. If capturing groups are used in the expression the results are stores in the special $Matches hashtable. Item 0 contains the entire line that triggered the match and each additional item contains the matching text from the capture group.

$text -Match '<Name>(.\*)</Name>'
$Matches

Output:

True

Name                           Value
---- -----
1                              host1
0                              <Name>host1</Name>

 

**Select-String -AllMatches**

The Select-String has an -AllMatches option that will return all matches (duh!). This returns a \[Microsoft.PowerShell.Commands.MatchInfo\] object. Match info is stored in the .Matches property, which is of type \[System.Text.RegularExpressions.Match\]. The .Matches.Value property contains the entire line that matched. The .Matches.Groups.Value property contains text within a capture group

$text | Select-String '<Name>(.\*)</Name>' -AllMatches | ForEach-Object { $\_.Matches }

Output:

Groups   : {<Name>host1</Name>, host1}
Success  : True
Captures : {<Name>host1</Name>}
Index    : 64
Length   : 18
Value    : <Name>host1</Name>

Groups   : {<Name>host2</Name>, host2}
Success  : True
Captures : {<Name>host2</Name>}
Index    : 147
Length   : 18
Value    : <Name>host2</Name>

 

**\[RegEx\]**

$re = \[Regex\]'<Name>(.\*)</Name>'
$result = $re.Matches($text)
$result

Output:

Groups   : {<Name>host1</Name>, host1}
Success  : True
Captures : {<Name>host1</Name>}
Index    : 64
Length   : 18
Value    : <Name>host1</Name>

Groups   : {<Name>host2</Name>, host2}
Success  : True
Captures : {<Name>host2</Name>}
Index    : 147
Length   : 18
Value    : <Name>host2</Name>
