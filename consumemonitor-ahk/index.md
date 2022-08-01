---
title: "ConsumeMonitor.ahk"
date: "2017-09-02"
categories: 
  - "autohotkey"
tags: 
  - "ahk"
  - "autohotkey"
---

These functions are to resize the active window to consume the entire active monitor (minus the task bar). I wrote this for a Java application that wouldn't maximize correctly if moved to a monitor running at a different resolution than the one it started on.

Code for ConsumeMonitor.ahk:

GetCurrentMonitor()
{
  SysGet, numberOfMonitors, MonitorCount
  WinGetPos, winX, winY, winWidth, winHeight, A
  winMidX := winX + winWidth / 2
  winMidY := winY + winHeight / 2
  Loop %numberOfMonitors%
  {
    SysGet, monArea, Monitor, %A\_Index%
    if (winMidX > monAreaLeft && winMidX < monAreaRight && winMidY < monAreaBottom && winMidY > monAreaTop)
      return %A\_Index%
  }
  SysGet, primaryMonitor, MonitorPrimary
  return "No Monitor Found"
}

ResizeWin(posX, posY, Width, Height)
{
  WinGetPos,X,Y,W,H,A

  If posX = ""
    posX := X

  If posY = ""
    posY := Y

  If Width = ""
    Width := W

  If Height = ""
    Height := H

  WinMove,A,,%posX%,%posY%,%Width%,%Height%
}

ConsumeMonitor()
{
  activeMonitor := GetCurrentMonitor()

  SysGet, workArea, MonitorWorkArea, %activeMonitor%
  workAreaWidth := workAreaRight - workAreaLeft
  workAreaHeight := workAreaBottom - workAreaTop

  ResizeWin(workAreaLeft, workAreaTop, workAreaWidth, workAreaHeight)
}

Code to include it in an existing script:

#Include %A\_ScriptDir%\\ConsumeMonitor.ahk

Code to assign it to a hotkey (Ctrl+Up Arrow):

^Up::ConsumeMonitor()
