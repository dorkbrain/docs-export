---
title: "blinker.sh"
date: "2017-08-27"
categories: 
  - "linux"
tags: 
  - "linux"
---

I wrote this to help identify (un/mis)labeled Red Hat and ESXi servers in a datacenter. This script will get a list of network interfaces and blink the lights on each one for 5 seconds using 'ethtool -p'. If the interface doesn't support this method it is removed from the list (ie: lots of on-board Broadcom NICs)

#!/bin/sh

### blinker.sh - Blink lights on all interfaces one at a time

iflist=
if \[ -e /usr/bin/netstat \]; then
  iflist=\`/usr/bin/netstat -i | awk 'NR>2 && !/^lo/ { print $1 }'\`
elif \[ -e /bin/netstat \]; then
  iflist=\`/bin/netstat -i | awk 'NR>2 && !/^lo/ { print $1 }'\`
elif \[ -e /bin/esxcli \]; then
  iflist=\`/bin/esxcli network nic list | awk 'NR>2 && !/^lo/ { print $1 }'\`
fi

echo ""
echo "Found interfaces:"
echo "$iflist"
echo ""
echo "Looping through interfaces... Press ^C to stop..."
echo ""
while \[ true \]
do
  for ifname in $iflist
  do
    echo "Blinking $ifname for 5 seconds..."
    ethtool -p $ifname 5
    if \[ $? -ne 0 \]; then
      echo "Error blinking $ifname, removing from the list..."
      iflist=\`echo $iflist|sed "s/$ifname//"|xargs\`
      if \[ ${#iflist} -gt 0 \]; then
        echo "New interface list:"
        echo $iflist
      else
        echo "No valid interfaces left, quitting..."
        echo ""
        exit 1
      fi
    fi
    echo "1 second delay..."
    sleep 1
  done
done
