---
title: "Reset a Forgotten root Password in CentOS"
date: "2017-08-27"
categories: 
  - "linux"
tags: 
  - "linux"
---

**CentOS 7**

1. From the boot menu press ''e'' to edit the default boot menu item.
2. Near the bottom find the ''linux16'' or ''linuxefi'' line.
3. Add ''rd.break enforcing=0'' to the end of the line.
4. Press ''ctrl+x'' to boot with this temporary config.
5. Type ''mount -o remount,rw /sysroot && chroot /sysroot'' to remount the partition as read/write and chroot to it.
6. Type ''passwd''
7. Enter your new password twice.
8. Type ''touch /.autorelabel''. This will force SELinux to relabel your system.
9. Type ''exit'' TWICE. This will exit chroot and continue the normal boot process.
10. The system will reboot and SELinux will relabel the system and will reboot again.
11. You're done! You can now login as root with your new password.

**CentOS 5/6**

1. Reboot your system.If you see ''Press any key to enter the menu'' go ahead and bash a key on the keyboard.
2. From the boot menu press ''a'' to edit the kernel command line.
3. Type ''\\ single'' and hit ENTER. \*\* Ensure you enter a space before the word single. \*\*
4. The system will boot into single-user mode and auto-login as root.
5. Type ''passwd''
6. Enter your new password twice.
7. Type ''touch /.autorelabel''. This will force SELinux to relabel your system.
8. Type ''reboot''
9. The system will reboot and SELinux will relabel the system and will reboot again.
10. You're done! You can now login as root with your new password.
