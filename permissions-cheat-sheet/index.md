---
title: "Permissions Cheat Sheet"
date: "2017-08-27"
categories: 
  - "linux"
tags: 
  - "linux"
---

| Perm | Value | Description |
| --- | --- | --- |
| Setuid | 4000 | \---s------ | On a dir: Typically ignored.  BSD can be configured to use this permission to behave like setgid. On an executable file: User executing a file will inherit uid of the owner Example:  chmod u+s /path/to/file |
| Setgid | 2000 | \------s--- | On a dir: New files and dirs created inside directory inherit the group of the parent On an executable file: User executing a file will inherit gid of the owner Example:  chmod g+s /path/to/file |
| sTicky | 1000 | d--------t | On a dir: Only root or an owner can delete or rename On a file: Ignored Example: chmod o+t /path/to/file |
| Read | 4 | \-r--r--r-- | On a dir: Read names of files inside but not metadata On a file: Read the contents Example: chmod u+r,g+r,o+r /path/to/file |
| Write | 2 | \--w--w--w- | On a dir: Create/Modify/Delete/Rename files inside On a file: Modify the contents Example: chmod u+w,g+w,o+w /path/to/file |
| eXecute | 1 | \---x--x--x | On a dir: Read file contents and metadata On a file: Execute the file Example: chmod u+x,g+x,o+x /path/to/file |

[Traditional Unix Permissions](https://en-wikipedia-org/wiki/File_system_permissions) [https://en-wikipedia-org/wiki/Setuid](https://en-wikipedia-org/wiki/Setuid)
