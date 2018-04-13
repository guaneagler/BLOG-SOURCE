---
title: Terminal note
date: 2018-04-13 15:22:46
tags:
	- terminal
	- git
	- bash
categroies: note
description: Some configuration for terminal.
---

## Color bash configuration
Open ~/.bashrc file, and uncomment 'force_color_prompt' label.
```
force_color_prompt=yes
```

## Update bash prompt.
Open ~/.bashrc file, Update variable *PS1*
```
if [ "$color_prompt" = yes ]; then
    PS1='${debian_chroot:+($debian_chroot)}\[\033[01;32m\]\u@\h\[\033[00m\]:\[\033[01;34m\]\w\[\033[00m\]\$ '
else
    PS1='${debian_chroot:+($debian_chroot)}\u@\h:\w\$ '
fi
```

## Git branch show configuration
Open ~/.bashrc file and add config below.
Please make sure the *unset color_prompt force_color_prompt* commands below the script.
```
if [ "$color_prompt" = yes ]; then
 PS1='${debian_chroot:+($debian_chroot)}\[\033[01;32m\]\u@\h\[\033[00m\]:\[\033[01;34m\]\w\[\033[01;31m\] $(parse_git_branch)\[\033[00m\]\$ '
else
 PS1='${debian_chroot:+($debian_chroot)}\u@\h:\w$(parse_git_branch)\$ '
fi
unset color_prompt force_color_prompt
```

## alias configuration
Add script below to ~/.bashrc
```
alias gs='git status'
```
