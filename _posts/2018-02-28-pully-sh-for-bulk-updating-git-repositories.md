---
layout: post
title: pully.sh for Bulk-Updating Git Repositories
date: 2018-02-28 22:53:00 +0900
categories:
  - Shell Script
tags:
  - Git
  - Suckless
references:
  - https://libgit2.github.io
  - https://stackoverflow.com/questions/918886
  - https://github.com/dgkim5360/desktop-configurations#(shameless plug)
---

## tl;dr
A bunch of Git repositories to check, fetch, and merge (or fast-forward pull) triggered me to write a simple program, which happened to be a shell script.

```sh
#!/bin/sh
# Written by Don Kim (dgkimdev@gmail.com)
#
# It reads "/path/to/repo:branchname" and updates it (via `git up`).
#
# If you don't have `git up`, check this and consider it;
# https://stackoverflow.com/questions/918886
while IFS=':' read -ra PAIR; do
	echo "${PAIR}"
	cd ${PAIR[0]} && git checkout ${PAIR[1]} && git up ${PAIR[1]}
done
```

It simply reads from the standard input, expecting strings including `:` as a delimeter, then digests each line as a repository path and a branch name.

`pully` needs the target list. I just save a file with name `.gitrepos` without special reasons.

```
/home/don/builds/dwm:master
/home/don/builds/st:master
/home/don/builds/slock:master
/home/don/builds/surf:surf-webkit2
/home/don/builds/tabbed:master
/home/don/.vim/pack/don/start/ale:master
/home/don/.vim/pack/don/start/goyo.vim:master
/home/don/.vim/pack/don/start/gruvbox:master
/home/don/.vim/pack/don/start/nerdtree:master
/home/don/.vim/pack/don/start/vim-commentary:master
/home/don/.vim/pack/don/start/vim-picker:master
/home/don/.vim/pack/don/start/vim-surround:master
/home/don/.vim/pack/don/start/vimtex:master
```

Finally I can update the repositories, sitting at `home` with the remote control.

```sh
$ ~/.local/bin/pully < ~/.gitrepos
```

As it seems lengthy to type, I added it as an alias `pully` in `.bashrc`.

So far, so good. My whole configuration files are on [my Github repository](https://github.com/dgkim5360/desktop-configurations).

## Why do the Git repos bother you?
Here Git repositories are for the package source code.

I am currently running Arch Linux, and in a progress to be a fan of the suckless environment. I am using dwm, st, slock, and still figuring out tabbed and surf. The suckless environment is and always wants to be light, minimal, and configurable (via C header files). Since I do not want fancy features, I really like it and am willing to deep dive into it (later).

Since the suckless environment requires compiling the source code, it is natural to clone their Git repository for the source code. Also I have several vim plugins, which are also simply Git repositories.

## Trials and errors
So, I now have two exclusive package schemes: One is of course pacman (the package manager), and the other is just Git repositories. It was painful to check every repository for fetching and pulling from the remote, and it does not seem right and here comes the motivation.

At the first attempt I reached libgit2 and tried to make C program, but it was not easy and seemed overkill to me. Moreover when I figured out that libgit2 did not offer exactly the same commands which Git supplied, I got more puzzled; e.g. there is no pull functionality for libgit2, which is actually fetch + merge.

I take back to the shell script and I am happy with it.

If you knows a better concept for managing the Git repositories of the package source code, please let me know.
