---
title: "colors.sh"
date: "2017-08-28"
categories: 
  - "linux"
tags: 
  - "linux"
---

#!/bin/bash
hr(){  # Prints a horizontal line the full terminal width
  if \[ $1 \]; then
    usechr="$1"
  else
    usechr=" "
  fi
  eval printf "%.0s'$usechr'" {1..${COLUMNS:-$(tput cols)}}
}

colors(){  # Prints ANSI sequences to change text/terminal color attributes
  declare -A colors
  colors=(
    \[black\]=0
    \[red\]=1
    \[green\]=2
    \[yellow\]=3
    \[blue\]=4
    \[magenta\]=5
    \[cyan\]=6
    \[white\]=7
  )

  while getopts ":f:b:sdiur" opt; do
    case $opt in
      f)
        #setaf $OPTARG
        if \[ -n ${colors\[$OPTARG\]} \]; then
          newcolor=${colors\[$OPTARG\]}
          printf "$(tput setaf $newcolor)"
        else
          echo "Invalid color: -$opt $OPTARG" >&2
        fi
        ;;
      b)
        #setab $OPTARG
        if \[ -n ${colors\[$OPTARG\]} \]; then
          newcolor=${colors\[$OPTARG\]}
          printf "$(tput setab $newcolor)"
        else
          echo "Invalid color: -$opt $OPTARG" >&2
        fi
        ;;
      s)
        #strong
        printf "$(tput bold)"
        ;;
      d)
        #dim
        printf "$(tput dim)"
        ;;
      i)
        #inverse
        printf "$(tput smso)"
        ;;
      u)
        #underline
        printf "$(tput smul)"
        ;;
      r)
        #reset to default
        printf "$(tput sgr0)"
        ;;
      \\?)
        echo "Invalid option: -$OPTARG" >&2
        ;;
    esac
  done
  OPTIND=0  # Reset OPTIND for next invocation of optargs
}

# Examples
#printf "$(colors -i)"; hr; printf "$(colors -r)"
#printf "$(colors -f black -b white)black on white$(colors -r)\\n"
#printf "$(colors -i)Inverse text$(colors -r)\\n"
