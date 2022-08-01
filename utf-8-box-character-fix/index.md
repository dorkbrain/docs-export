---
title: "UTF-8 Box Character Fix"
date: "2017-09-02"
categories: 
  - "linux"
tags: 
  - "linux"
---

This addresses issues displaying box/line drawing characters in PuTTY.

- In your PuTTY session settings go to Window -> Translation.
- Change the Remote character set to UTF-8.
- On your linux box open ~/.bash\_profile with your favorite editor.
- At the bottom add "export NCURSES\_NO\_UTF8\_ACS=1" without quotes.
- Save and exit.
- For the change to take place in your current SSH session you'll need to type "export NCURSES\_NO\_UTF8\_ACS=1" at the prompt. Otherwise just logoff and back on.

If you'd like this setting to be system-wide place the export statement at the bottom of a new .sh file in /etc/profile.d (ie: /etc/profile.d/utf8fix.sh)
