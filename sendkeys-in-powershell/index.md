---
title: "SendKeys in PowerShell"
date: "2017-09-03"
categories: 
  - "powershell"
tags: 
  - "powershell"
---

\[void\] \[System.Reflection.Assembly\]::LoadWithPartialName("Microsoft.VisualBasic")
\[void\] \[System.Reflection.Assembly\]::LoadWithPartialName("System.Windows.Forms")
 
\[Microsoft.VisualBasic.Interaction\]::AppActivate("Some Window Title")
\[System.Windows.Forms.SendKeys\]::SendWait("Some text~")

 

**Key Modifiers**

| **Key** | **Code** |
| :-- | :-- |
| Shift | + |
| Ctrl | ^ |
| Alt | % |

 

**Key Codes**

| **Key** | **Code** |
| :-- | :-- |
| BACKSPACE | {BACKSPACE}, {BS}, or {BKSP} |
| BREAK | {BREAK} |
| CAPS LOCK | {CAPSLOCK} |
| DEL or DELETE | {DELETE} or {DEL} |
| DOWN ARROW | {DOWN} |
| END | {END} |
| ENTER | {ENTER}or ~ |
| ESC | {ESC} |
| HELP | {HELP} |
| HOME | {HOME} |
| INS or INSERT | {INSERT} or {INS} |
| LEFT ARROW | {LEFT} |
| NUM LOCK | {NUMLOCK} |
| PAGE DOWN | {PGDN} |
| PAGE UP | {PGUP} |
| PRINT SCREEN | {PRTSC} (reserved for future use) |
| RIGHT ARROW | {RIGHT} |
| SCROLL LOCK | {SCROLLLOCK} |
| TAB | {TAB} |
| UP ARROW | {UP} |
| F1 | {F1} |
| F2 | {F2} |
| F3 | {F3} |
| F4 | {F4} |
| F5 | {F5} |
| F6 | {F6} |
| F7 | {F7} |
| F8 | {F8} |
| F9 | {F9} |
| F10 | {F10} |
| F11 | {F11} |
| F12 | {F12} |
| F13 | {F13} |
| F14 | {F14} |
| F15 | {F15} |
| F16 | {F16} |
| Keypad add | {ADD} |
| Keypad subtract | {SUBTRACT} |
| Keypad multiply | {MULTIPLY} |
| Keypad divide | {DIVIDE} |
