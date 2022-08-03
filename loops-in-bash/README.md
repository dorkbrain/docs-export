---
title: "Loops in Bash"
date: "2017-08-27"
categories: 
  - "linux"
tags: 
  - "linux"
---

for i in /var/log/\*; do
  echo $i
done

for i in $( ls /var/log/\* ); do
  echo $i
done

for i in item1 item2 item3; do
  echo $i
done

items="item1
item2
item3"
for i in $items; do
  echo $i
done

for i in {1..10}; do
  echo $i
done

ctr=0
while \[ $ctr -lt 10 \]; do
  echo $i
  let ctr+=1
done

ctr=0
until \[ $ctr -lt 10 \]; do
  echo $i
  let ctr+=1
done
