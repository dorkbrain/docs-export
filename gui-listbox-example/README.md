---
title: "GUI ListBox Example"
date: "2017-09-02"
categories: 
  - "autohotkey"
tags: 
  - "ahk"
  - "autohotkey"
---

I use a similar set of scripts to autologin to my vSphere Client for multiple servers. I make Windows launch onLogin.ahk during startup so it's always running in the background. Both scripts need to reside in the same directory.

\*\*onLogin.ahk\*\* monitors the list open windows for the vSphere Client login prompt. When it finds the prompt it then starts the vmMenu.ahk script and waits for it to close before starting to monitor again.

\*\*vmMenu.ahk\*\* displays a listbox populated with a list of servers and IP addresses. Once an entry is double-clicked it will then fill in the username, password, and IP address. Then it'll trigger the Login button before finishing the script. The script will wait for the login prompt to disappear before closing.

**onLogin.ahk**

#SingleInstance Force
#Persistent

; Trigger on window load
SetTimer, OnWindowLoad, 100
Return

;Subroutine: OnWindowLoad
OnWindowLoad:
  ; VMware vSphere Client
  If WinExist("ahk\_class WindowsForms10.Window.8.app.0.259f9d2") {
    RunWait %A\_ScriptDir%\\vmMenu.ahk
  }
Return

**vmMenu.ahk**

#SingleInstance Force

; Init array and add choices
Choices := Object()
Choices.Insert("Server1: 10.22.41.76")
Choices.Insert("Server2: 10.22.41.8")
Choices.Insert("Server3: 10.22.41.14")

; Join array items with pipe char for use in a listbox
LongestString := ""
for index, element in Choices {
  ; Determine the longest string to calculate the size of our form/listbox
  if ( StrLen(element) > StrLen(LongestString) ) {
    LongestString := element
  }
  
  ; Add the element
  ChoiceList .= element
  if ( index < Choices.MaxIndex() ) { 
    ChoiceList .= "|" 
  }
}

; Create new GUI
Gui, +AlwaysOnTop -Border +Caption -Disabled -LastFound -MinimizeBox -MaximizeBox -OwnDialogs -Resize +SysMenu +Theme +ToolWindow
Gui, Font, s12, Consolas

; Create hidden label representing the longest string from our Choices array
Gui, Add, Text, Hidden, %LongestString%

; Calculate width and height in pixels of the list items so we can size the control and window properly
GuiControlGet, MyFont, Pos, %LongestString%
MyHeight := (MyFontH \* Choices.MaxIndex()) + 5
MyWidth := MyFontW + 10
Gui, Add, ListBox, vMyListBox gMyListBox x0 y0 w%MyWidth% h%MyHeight% Choose1, %ChoiceList%

; Show us the window
Gui, Show, Center w%MyWidth% h%MyHeight%, VM Servers
Return

MyListBox:
  if A\_GuiEvent <> DoubleClick
    return
  
  GuiControlGet, MyListBox
  Gui, Destroy
  
  ServerName := Trim(SubStr(MyListBox, 1, InStr(MyListBox, ":")-1))
  ServerAddress := Trim(SubStr(MyListBox, InStr(MyListBox, ":")+1, StrLen(MyListBox)-InStr(MyListBox, ":")+1))
  WinActivate, VMware vSphere Client
  WinWaitActive, VMware vSphere Client
  SendInput !n^a
  SendInput %ServerAddress%

  If IsLabel("Case-" . ServerName)
  Loop 1 {
     Goto Case-%ServerName%
  Case-Server1:
     SendInput !u^a
     SendInput root
     SendInput !p^a
     SendInput {Raw}password1
     Break
  Case-Server2:
     SendInput !u^a
     SendInput root
     SendInput !p^a
     SendInput {Raw}password2
     Break
  Case-Server3:
     SendInput !u^a
     SendInput joeuser
     SendInput !p^a
     SendInput {Raw}password3
     Break
  } Else {
     SoundBeep 
  }
  
  SendInput !l
  WinWaitClose, VMware vSphere Client
  ExitApp
return

GuiClose:
  Gui, Destroy
  WinWaitClose, VMware vSphere Client
  ExitApp
