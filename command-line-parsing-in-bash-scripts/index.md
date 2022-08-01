---
title: "Command Line Parsing in Bash Scripts"
date: "2017-10-16"
categories: 
  - "linux"
tags: 
  - "linux"
---

Simple example of parsing the command line in a bash script:

#!/bin/bash

EXTRA\_ARGS=()
VERBOSE=0  # Set default value for the verbose flag
while \[\[ $# -gt 0 \]\]; do
  case $1 in
    -h|--host)
        HOST="$2"
        shift
        shift
        ;;
      -u|--username)
        USER="$2"
        shift
        shift
        ;;
      -v|--verbose)
        VERBOSE=1
        shift
        ;;
      --help)
        echo "$(basename $0) \[-h|--host <hostname>\] \[-u|--user <username>\] \[-v|--verbose\] \[arg1\] \[arg2\] \[...\]"
        exit 1
        ;;
      \*)
        EXTRA\_ARGS+=("$1")
        shift
        ;;
  esac
done

echo "HOST = ${HOST}"
echo "USER = ${USER}"
echo "VERBOSE = ${VERBOSE}"
if \[\[ -n $EXTRA\_ARGS \]\]; then
  i=0
  for thisarg in ${EXTRA\_ARGS\[\*\]}; do
    (( i++ ))
    echo "EXTRA${i} = ${thisarg}"
  done
fi
