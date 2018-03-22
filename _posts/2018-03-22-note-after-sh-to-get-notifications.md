---
layout: post
title: note-after.sh to Get Notifications in Linux
date: 2018-03-22 15:15:00 +0900
categories:
  - Shell Script
tags:
  - Linux
  - Notification
references:
  - ttps://unix.stackexchange.com/questions/197792
  - https://stackoverflow.com/questions/22009364
  - https://github.com/dgkim5360/desktop-configurations#(shameless plug)
---

## tl;dr

```bash
#!/usr/bin/sh
# Written by Don Kim (dgkimdev@gmail.com)
#
# It executes whole arguments and pops a notification after finished.
# Since it uses notify-send, libnotify is required.

dowork() {
	echo "$*"
	"$@"
}
notesuccess() {
	notify-send "DONE" "$*" -a "noteafter"
}
notefailure() {
	notify-send "FAILED" "$*" -a "noteafter" -u "critical"
}

{
	dowork "$@" && notesuccess "$@"
} || {
	notefailure "$@"
}
```

Of course, I use it abbreviated as `na sudo pacman -Syyuu` via an alias.

My other scripts and configuration files are on [my Github repository](https://github.com/dgkim5360/desktop-configurations).

## Motivation: the rust program `alert-after` 

I happened to find [this project](https://github.com/frewsxcv/alert-after), which delivers a desktop notification after a command finishes executing. It supports Linux, MacOS, and Windows.

Although this program looks good to me, I just don't want the support for MacOS and Windows. So I wonder how to get the same but minimal functionality for my Linux environment, and the conclusion is that the shell script is enough to do this. Today I learned that

* Shell script can take and handle arguments
* and can handle errors through conditional expressions

Those were all I needed to implement the functionalities.
