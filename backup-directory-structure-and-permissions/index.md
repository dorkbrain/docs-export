---
title: "Backup Directory Structure and Permissions"
date: "2017-08-27"
categories: 
  - "linux"
tags: 
  - "linux"
---

I recently had duplicate a directory structure and its permissions from one server to another without copying any files. The servers couldn't talk to each other directly so I had to TAR what I needed, copy it to a thumb drive, then from the thumb drive to the destination server.

\# Find the USB device name
lsblk -f

# Create a directory and mount the USB device to it
mkdir /mnt/sdb1
mount /dev/sdb1 /mnt/sdb1

# Find only directories under /var/ftproot and send the output to the command line of tar
find /var/ftproot -type d -print0 | xargs -0 tar cvf /mnt/sdb1/ftproot-structure.tar --no-recursion

# Unmount the thumb drive
umount /mnt/sdb1

 

\# Find the USB device name
lsblk -f

# Create a directory and mount the USB device to it
mkdir /mnt/sdb1
mount /dev/sdb1 /mnt/sdb1

# Extract the directories and permissions from the tar
tar xvf /mnt/sdb1/ftproot-structure.tar -C /var

# Unmount the thumb drive
umount /mnt/sdb1
