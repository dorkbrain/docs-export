---
title: "rootcheck Function"
date: "2017-08-27"
categories: 
  - "linux"
tags: 
  - "linux"
---

Call this function at the beginning of a bash script to ensure it runs as root. The ''sudo'' statement can be replaced with a message telling the user to run as root if automatic execution is not desired.

rootcheck () {
  if \[ $(id -u) != "0" \]
  then
    sudo "$0" "$@"
    exit $?
  fi
}
