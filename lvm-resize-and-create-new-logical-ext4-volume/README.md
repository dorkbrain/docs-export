---
title: "LVM - Resize and Create New Logical ext4 Volume"
date: "2017-08-28"
categories: 
  - "linux"
tags: 
  - "linux"
---

In this example I shrink the /home volume and create a new volume for /opt.

**Be sure to backup your data!! If you resize the volume you're shrinking to less than the amount of data you have on it data WILL be destroyed!**

\# List all logical volumes and make note of the VG Name (volume group)
# and LV Name (logical volume). Also make sure you remember the original
# size for the logical volume you are shrinking so you know how big to make
# the new logical volume.
lvdisplay –m

# Stop any processes/services that may use both /opt and /home

# Rename /opt and create an empty /opt directory to mount to 
mv /opt /opt.old
mkdir /opt

# Unmount /home to shrink the volume
umount /home 

# We’re required to do a fs check before a resize operation 
e2fsck -f /dev/mapper/<VGname>-<LVname>

# Resize the volume /home from 300G to 200G (freeing up 100G)
resize2fs /dev/mapper/<VGname>-<LVname> 200G

# Resize the logical volume to 200G
lvreduce -L 200G /dev/mapper/<VGname>-<LVname>

# Check the fs again and remount /home
e2fsck -f /dev/mapper/<VGname>-<LVname>
mount /home

# Create a new logical volume called 'opt' in the <VGname> volume group
lvcreate -L 100G -n opt <VGname>

# Format the new volume with ext4
mkfs -t ext4 /dev/mapper/opt

# Mount the new /opt volume
mount –t /dev/mapper/<VGname>-opt /opt

# Move the data from the /opt.old directory to the new /opt volume
mv /opt.old/\* /opt

# Make sure the /opt.old directory is empty then delete it
ls –l /opt.old
rmdir /opt.old

# Add to /etc/fstab so it mounts after a reboot
/dev/mapper/<VGname>-opt /opt ext4 defaults 1 2

# Restart any processes/services you stopped earlier, or reboot now.
