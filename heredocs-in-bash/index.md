---
title: "Heredocs in Bash"
date: "2017-08-27"
categories: 
  - "linux"
tags: 
  - "linux"
---

\# Heredoc
cat<<ENDVAR
  test1
  test2
  test3
ENDVAR

# Ignore tabs but keep spaces
cat<<-ENDVAR
  test1
  test2
  test3
ENDVAR

# Don't substitute variables
cat<<'ENDVAR'
  test1
  test2
  test3
ENDVAR

# Assign to variable
MYVAR=$(cat<<ENDVAR
  test1
  test2
  test3
ENDVAR
)
