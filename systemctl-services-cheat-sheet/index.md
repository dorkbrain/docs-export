---
title: "SysVInit/SystemD Cheat Sheet"
date: "2017-08-27"
categories: 
  - "linux"
tags: 
  - "linux"
---

| **sysvinit command** | **systemd command** | **notes** |
| --- | --- | --- |
| service sshd start | systemctl start sshd | Start the sshd service |
| service sshd stop | systemctl stop sshd | Stop the sshd service |
| service sshd restart | systemctl restart sshd | Restart the sshd service |
| service sshd reload | systemctl reload sshd | Reload the sshd service |
| service sshd condrestart | systemctl condrestart sshd | Restarts sshd if it's already running |
| service sshd status | systemctl status sshd | Check the status of sshd |
| chkconfig --list | systemctl list-unit-files --type=service | List all services and their status |
| chkconfig sshd on | systemctl enable sshd | Enable the sshd service |
| chkconfig sshd off | systemctl disable sshd | Disable the sshd service |
| chkconfig --list sshd | ls /etc/systemd/system/\*.wants/sshd.service | Show all runlevels/targets sshd is enabled in |
| chkconfig --add sshd | systemctl daemon-reload | Used to create new service files |

sysvinit service scripts are stored in /etc/init.d or /etc/rc.d/init.d and have no extension.

systemd service files are stored in /etc/systemd/system/\*.wants directories. Service files need to have a .service extension.

## Runlevels

<table style="border-collapse: collapse; width: 100%;" border="1"><tbody><tr><td style="width: 33.3333%;"><strong>sysvinit runlevel</strong></td><td style="width: 33.3333%;"><strong>systemd target name</strong></td><td style="width: 33.3333%;"><strong>notes</strong></td></tr><tr><td style="width: 33.3333%;">0</td><td style="width: 33.3333%;">runlevel0, poweroff</td><td style="width: 33.3333%;">Shut down and power off the system</td></tr><tr><td style="width: 33.3333%;">1</td><td style="width: 33.3333%;">runlevel1, rescue</td><td style="width: 33.3333%;">Setup rescue shell</td></tr><tr><td style="width: 33.3333%;">2, 3, 4</td><td style="width: 33.3333%;">runelevel2, runlevel3, runlevel4, multi-user</td><td style="width: 33.3333%;">Non-graphical multi-user</td></tr><tr><td style="width: 33.3333%;">5</td><td style="width: 33.3333%;">runlevel5, graphical</td><td style="width: 33.3333%;">Graphical multi-user</td></tr><tr><td style="width: 33.3333%;">6</td><td style="width: 33.3333%;">runlevel6, reboot</td><td style="width: 33.3333%;">Reboot the system</td></tr></tbody></table>

### Commands

<table style="border-collapse: collapse; width: 100%;" border="1"><tbody><tr><td style="width: 33.3333%;"><strong>sysvinit</strong></td><td style="width: 33.3333%;"><strong>systemd</strong></td><td style="width: 33.3333%;"><strong>notes</strong></td></tr><tr><td style="width: 33.3333%;">runlevel</td><td style="width: 33.3333%;">systemctl list-units --type target</td><td style="width: 33.3333%;">List runlevels</td></tr><tr><td style="width: 33.3333%;">cat /etc/inittab</td><td style="width: 33.3333%;">systemctl get-default</td><td style="width: 33.3333%;">Default runlevel</td></tr><tr><td style="width: 33.3333%;">telinit &lt;runlevel#&gt;</td><td style="width: 33.3333%;">systemctl isolate &lt;name&gt;.target</td><td style="width: 33.3333%;">Change the runlevel</td></tr><tr><td style="width: 33.3333%;">nano or vi /etc/inittab</td><td style="width: 33.3333%;">systemctl set-default &lt;name&gt;.target</td><td style="width: 33.3333%;">Change the default runlevel</td></tr></tbody></table>
