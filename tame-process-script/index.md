---
title: "Tame-Process Script"
date: "2017-09-03"
categories: 
  - "powershell"
tags: 
  - "powershell"
---

I wrote this to run via Task Scheduler under the SYSTEM account when the computer boots to lower the priority/affinity of CPU hungry background apps and to kill useless background processes. Every 60 seconds it checks the list of running processes, kills the ones in $killList array, then lowers the priority and affinity of the ones in the $tameList array.

\# Check running processes every xx seconds
$cycleSeconds = 60
 
# Array of processes to kill every cycle
$killList = @(
	"proc1"
	"proc2"
)
 
# Array of processes to change priority and affinity every cycle
$tameList = @(
	"proc3"
	"proc4"
)
 
# The priority to assign to processes in $tameList (see table below for valid values)
$tamePriority = "Idle"
 
# The affinity to assign to the processes in $tameList (see table below to calculate affinity)
$tameAffinitty = 2
 
#################################################################################
# PriorityClass values       # ProcessorAffinity values (add CPU values together)
# 	RealTime             # ie: Affinity value for CPU0 + CPU2 = 5
# 	High                 # 	CPU0 = 1
# 	AboveNormal          # 	CPU1 = 2
# 	Normal               # 	CPU2 = 4
# 	BelowNormal          # 	CPU3 = 8
# 	Idle                 # 	etc.
#################################################################################
 
 
# Begin infinite loop
While ( $true ) {
	# Stop any processes in the kill list
	Get-Process $killList -ErrorAction SilentlyContinue | Stop-Process -Force -ErrorAction SilentlyContinue
 
	# Loop through each process in the array defined above
	Get-Process $tameList -ErrorAction SilentlyContinue | ForEach-Object {
		$\_.PriorityClass = $tamePriority       # Set the priority
		$\_.ProcessorAffinity = $tameAffinity   # Set the affinity
	}
 
	Start-Sleep -Seconds $cycleSeconds  # Wait xx seconds and do it all over again
}  # End infinite loop
 
 
# The While statement above will continue to loop as long as the expression
# between ( and ) evaluates to True, a non-zero number, or a non-empty object.
