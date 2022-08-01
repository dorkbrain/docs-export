---
title: "Get-WMILoggedOnUser"
date: "2017-09-02"
categories: 
  - "powershell"
tags: 
  - "powershell"
---

Function Get-WMILoggedOnUser {
<#
.SYNOPSIS
    Function to get all logged on sessions (numbers) and coresponding users (domain name) on a local or remote system with WMI
 
.DESCRIPTION
    Function to get all logged on sessions (numbers) and coresponding users (domain name) on a local or remote system with WMI
 
	Gets the Win32\_LogonSession and the associated Win32\_LoggedOnUser information from WMI.
	Matches the user to the session by sessionid
	Creates a link between the LogType number to LogonTypeName name for the session logon type
 
	Returns an PSCustomObject:
		User properties: Domain,Name and SID
		UserSession properties: StartTime,LogonID,LogonType,LogonTypeName and the ComputerName
 
.PARAMETER ComputerName
	Specifies the computer against which you want to run the management operation.
	The value can be a fully qualified domain name, a NetBIOS name, or an IP address.
	Use the local computer name, use localhost, or use a dot (.) to specify the local computer.
	The local computer is the default. When the remote computer is in a different domain from the user,
	you must use a fully qualified domain name. This parameter can also be piped to the cmdlet.
 
.PARAMETER Logontype
 
	Parameter to select the returned logontype(s)
 
	See MSDN documentation for the WMI Win32\_LogonSession class Property Logontype
 
	Win32\_LogonSession.Logontype is Numeric value that indicates the type of logon session.
 
	0	Used only by the System account.
 
	2	Interactive
		Intended for users who are interactively using the machine, such as a user
		being logged on by a terminal server, remote shell, or similar process.
 
	3	Network
		Intended for high-performance servers to authenticate clear text passwords.
		LogonUser does not cache credentials for this logon type.
 
	4	Batch
		Intended for batch servers, where processes can be executed on behalf of a user
		without their direct intervention; or for higher performance servers that process many
		clear-text authentication attempts at a time, such as mail or web servers. LogonUser
		does not cache credentials for this logon type.
 
	5	Service
		Indicates a service-type logon. The account provided must have the service privilege enabled.
 
	6	Proxy
		Indicates a proxy-type logon.
 
	7	Unlock
		This logon type is intended for GINA DLLs logging on users who are interactively
		using the machine. This logon type allows a unique audit record to be generated
		that shows when the workstation was unlocked.
 
	8	NetworkCleartext
		Windows Server 2003 and Windows XP:  Preserves the name and password in the authentication packages,
		allowing the server to make connections to other network servers while impersonating the client.
		This allows a server to accept clear text credentials from a client, call LogonUser, verify that
		the user can access the system across the network, and still communicate with other servers.
 
	9	NewCredentials
		Windows Server 2003 and Windows XP:  Allows the caller to clone its current
		token and specify new credentials for outbound connections. The new logon session
		has the same local identify, but uses different credentials for other network connections.
 
	10	RemoteInteractive
		Terminal Services session that is both remote and interactive.
 
	11	CachedInteractive
		Attempt cached credentials without accessing the network.
 
	12	CachedRemoteInteractive
		Same as RemoteInteractive. This is used for internal auditing.
 
	13	CachedUnlock
		Workstation logon.
 
.PARAMETER Credential
    Allows alternate Credentials to be used in query
 
.EXAMPLE
    Get-WMILoggedOnUser
 
	Returns all types of logged on User sessions on the local machine (computer)
 
.EXAMPLE
    Get-WMILoggedOnUser -Computername 'localhost'  -LogonType 'All'
 
	Same as example 1: Returns all types of logged on User sessions on the local machine (computer)
 
.EXAMPLE
    Get-WMILoggedOnUser -Computername 'Server1','Server2' -LogonType 'Interactive'
 
	Returns only logged on Users with logon type of 'Interactive' (number 2)
#>
 
    \[Cmdletbinding()\]
    param (
        \[Parameter(Position=0,ValueFromPipeline=$True,ValueFromPipelineByPropertyName=$True)\]
        \[Alias('\_\_Server','DNSHostname','IPAddress','Name')\]
        \[String\[\]\]$ComputerName = $ENV:COMPUTERNAME,
 
		\[Parameter(Position=1)\]
		\[ValidateSet('0','LocalSystem','2','Interactive','3','Network','4','Batch','5',
		'Service','6','Proxy','7','Unlock','8','NetworkCleartext','9','NewCredentials',
		'10','RemoteInteractive','11','CachedInteractive','12','CachedRemoteInteractive',
		'13','CachedUnlock','All')\]
		\[String\[\]\]$LogonType = @('0','2','3','4','5','6','7','8','9','10','11','12','13'), # All
        \[parameter()\]
        \[Alias('RunAs')\]
        \[System.Management.Automation.Credential()\]$Credential = \[System.Management.Automation.PSCredential\]::Empty     
    )
 
    Begin{
            #wmiparams
            $wmiParam = @{
                ErrorAction = 'Stop'
            }
            If ($PSBoundParameters\['Credential'\]) {
                $wmiParam.Credential = $Credential
            }
            # define LogOnType hashtable for to convert Numbers into Text
            $HashLogonType = @{ 
                '0'='LocalSystem' 
                '2'='Interactive'
                '3'='Network'
                '4'='Batch'
                '5'='Service'
                '6'='Proxy'
                '7'='Unlock'
                '8'='NetworkCleartext'
                '9'='NewCredentials'
                '10'='RemoteInteractive'
                '11'='CachedInteractive'
                '12'='CachedRemoteInteractive'
                '13'='CachedUnlock'
            }
 
    } # end Begin block
 
    Process {
        Try {
            ForEach($CurComputerName in $ComputerName) {
                $wmiParam.Computername = $CurComputerName
                $wmiParam.Class = 'Win32\_LogonSession'
                $LogonSessions = Get-WmiObject @wmiParam
                $wmiParam.Remove('Class')
                ForEach($LogonSession in $LogonSessions) {
                    Try {
                        $wmiParam.Query = "Associators of {Win32\_LogonSession.LogonId=$($LogonSession.LogonId)} Where AssocClass=Win32\_LoggedOnUser Role=Dependent"
                        $LoggedOnUser = Get-WmiObject @wmiParam | 
                            Select-Object Domain,Name,SID,StartTime,LogonID,LogonType,LogonTypeName,ComputerName
                        If ($LoggedOnUser.Name) {
                            $LoggedOnUser.StartTime = \[Management.ManagementDateTimeConverter\]::ToDateTime($LogonSession.starttime)
                            $LoggedOnUser.LogonID = $LogonSession.LogonID
                            $LoggedOnUser.LogonType = $LogonSession.logontype
                            $LoggedOnUser.LogonTypeName = $HashLogonType\[\[String\]$LogonSession.logontype\]
                            $LoggedOnUser.ComputerName = $CurComputerName
 
				            # Filter selected LogonTypes to report
				            If($LogonType -contains \[String\]$LoggedOnUser.LogonType -or $LogonType -contains $LoggedOnUser.LogonTypeName) {
					            # return result object
					            $LoggedOnUser
				            }
                        } Else {
                            #Write-Warning ("{0}: Cannot find associated Logon Session!" -f $LogonSession.logonId)
                        }
                    } Catch {
                        #Write-Warning ("{0}: {1}" -f $Logonsession.logonid,$\_.exception.message)
                    }
                } # end  ForEach $LogonSession
                $wmiParam.Remove('Query')
            }  # end ForEach $Computer 
        } catch \[exception\] {
            #Write-Warning ("{0}" -f $\_.exception.message)
        }
    } # end process block
    End {}
}
