---
title: My .screenrc
date: 2019-11-02 09:17:41
tags:
- linux
- screen
categories:
- tooling
---

Save the following as ~/.screenrc

```
# ctrl+a q unblock scrolling
altscreen on # erase screen when quit apps
defutf8 on
defc1 off # treat input chars 128 ~ 159 as control functions
defflow on
activity "activity in %n (%t)"
bell_msg "bell in %n (%t)"
vbell on
startup_message off
nethack on
shell $SHELL
defscrollback 10000

# using Ctrl+Left & Ctrl+Right
bindkey ^[[1;5D prev
bindkey ^[[1;5C next
bindkey \033[D prev
bindkey \033[C next

# encoding
defencoding utf8
bind b encoding big5 utf8
bind u encoding utf8 utf8
defbce on # erase background with current bg color
cjkwidth off
caption always "%n(%t)"

screen -t coding 0
screen -t coding 1
screen -t coding 2
screen -t build_code 3
screen -t execution 4
```
