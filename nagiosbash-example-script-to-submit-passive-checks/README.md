---
title: "Nagios/Bash - Example Script to Submit Passive Checks"
date: "2017-11-03"
categories: 
  - "linux"
tags: 
  - "linux"
---

**submit\_passive\_check.sh** - Contains functions to easily submit passive Nagios checks.  Very useful for servers that cannot run the NRPE agent.  The only external requirements are curl, awk, and grep which are installed by default on the majority of Linux based distros (ie: CheckPoint Gaia, F5, etc).  A simple example of checking the current CPU load is included at the bottom of the script.

#!/bin/bash

get\_nagios\_state() {
# Usage:
#        get\_nagios\_state <current> <warn> <crit>

# Checks a value against warning/critical thresholds and returns the correct Nagios state code

  awk -v cur="$1" -v warn="$2" -v crit="$3" '
  BEGIN {
    state\_ok=0;
    state\_warning=1;
    state\_critical=2;
    state\_unknown=3;
    exit\_state=state\_unknown;
    
    if ( cur < warn )
      exit\_state=state\_ok;
    else if ( cur >= warn )
      exit\_state=state\_warning;
      if ( cur >= crit )
        exit\_state=state\_critical;
        
    exit exit\_state;
  }'
  
  return $?
}

submit\_passive\_check() {
# Usage:
#        submit\_passive\_check <nagios\_url> <user> <pass> <host> <service> <state> <output> \[perfdata\]

# Logs in as the Nagios user and submits a passive check via CGI

  NAGIOS\_URL="$1"
  NAGIOS\_USER="$2"
  NAGIOS\_PASS="$3"
  CHECK\_HOST="$4"
  CHECK\_SERVICE="$5"
  CHECK\_STATE="$6"
  CHECK\_OUTPUT="$7"
  CHECK\_PERFDATA="$8"

  CURL\_OPTS="--silent --head"

  CURL\_LOG=$(curl ${CURL\_OPTS} --user "${NAGIOS\_USER}:${NAGIOS\_PASS}" \\
       --data-urlencode "cmd\_typ=30" \\
       --data-urlencode "cmd\_mod=2" \\
       --data-urlencode "host=${CHECK\_HOST}" \\
       --data-urlencode "service=${CHECK\_SERVICE}" \\
       --data-urlencode "plugin\_state=${CHECK\_STATE}" \\
       --data-urlencode "plugin\_output=${CHECK\_OUTPUT}" \\
       --data-urlencode "performance\_data=${CHECK\_PERFDATA}" \\
       "${NAGIOS\_URL}/cgi-bin/cmd.cgi")

  echo "${CURL\_LOG}" | grep -q "successfully submitted"
  RESULT=$?
  if \[\[ $RESULT -ne 0 \]\]; then
    echo -e "ERROR: Error submitting passive check to Nagios CGI!\\n\\n${CURL\_LOG}" >&2
    return 1
  fi

  return 0
}

# Example: Get current CPU load and report it back to a Nagios server
#   my\_hostname=$(hostname -s | awk '{print tolower($0)}')
#   cpuload=$(cat /proc/loadavg | cut -d' ' -f1)
#   nagios\_state=$(get\_nagios\_state ${cpuload} 0.50 0.75; echo $?)
#   submit\_passive\_check http://192.168.1.100/nagios passiveuser mypassword ${my\_hostname} cpuload ${nagios\_state} ${cpuload} "load=${cpuload};0.50;0.75"
