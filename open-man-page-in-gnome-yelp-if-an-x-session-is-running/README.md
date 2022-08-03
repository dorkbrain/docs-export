---
title: "Open MAN page in Gnome Yelp if an X Session is Running"
date: "2017-09-23"
categories: 
  - "linux"
tags: 
  - "linux"
---

Toss this in your ~/.bashrc to use the Gnome Yelp help viewer to open a man page.  This replaces the man command with a function and only uses the CLI version of man if there's no running X session.

#!/bin/bash
man() {
  if \[\[ $(xhost) \]\]; then
    { yelp "man:$@" & } >/dev/null 2>&1 ; disown
  else
    /usr/bin/man "$@"
  fi
}

If an X session is running it instead opens the man page in Yelp as a background job so you can keep working in your shell while Yelp is open.
