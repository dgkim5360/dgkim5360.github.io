---
layout: post
title: Rolling Back of Git Commits, Only When Working Alone
date: 2017-06-11 17:40:00 +0900
categories:
  - Git
tags:
  - tutorial
references:
  - https://stackoverflow.com/questions/4114095/how-to-revert-git-repository-to-a-previous-commit
  - https://git-scm.com/docs/git-checkout
  - https://git-scm.com/docs/git-reset
---

## tl;dr

This post summarizes git usage of reverting git commits: Rolling back, edit and fix again, then re-commit to master. `git checkout` and `git reset` does this job.

**Warning** Please use only when you work alone. The usage introduced here completely overwrites master, which means the history (between the latest commit and the commit of the roll-back point) is erased.

## The basics

Truly basic commands are listed below.

```shell
# initiate a git repository
git init

# do some work, for example
mkdir myfolder
pip freeze > myfolder/requirements.txt

# check the status
git status

# add the modified or the created
git add . # add all (tracked + untracked)
git add -u # add only tracked files

# commit
git commit -m "your commit message"
git commit -am "" # same as: git add -u && git commit -m ""
# then the named branch "master" refers to the latest commit

# push if you use a github repository
git remote add origin https://github.com/yourid/repo.git
git push origin master
```

## Roll back 1: checkout

`git checkout` moves HEAD to a certain commit, preserving every work. Once you `checkout` where you want, a commit of additional work results in another branch (temporary if you are in "detached HEAD" state). Then merging the current HEAD to master will do the job. [The official document about "detached HEAD"](https://git-scm.com/docs/git-checkout#_detached_head) is recommended to read.

```shell
git checkout master^ # just trip to the last commit before latest one (which is master), no branching
git checkout master # return to master, changes in detached HEAD state will be removed
git checkout commitid
# then HEAD refers to that commit

# you can name it by creating a new branch
git checkout -b newbranch
git branch # then check where we are

# after more edits and commits, overwrite this point (detached HEAD) to master
git checkout -B master # override master
# if you want push, use -f flag to force
git push -f origin master
```

## roll back 2: reset

`git reset` immediately reverts the repository to a certain commit, forgetting every commit history in between. `--soft` flag preserves the file contents, but `--hard` does not. Below are simple examples from [the official document](https://git-scm.com/docs/git-reset#_examples). See docs for more sophisticated usage of `git reset`.

```shell
# undo the add operation
git add SOMETHING
git reset SOMETHING

# undo the commit operation, but the file edit of the last commit is preserved
git commit # oh, I did something wrong
git reset --soft HEAD^ # remove HEAD history
git status # check the last commit is gone

# completely undo the commits, rolling back all files to a certain commit
git reset --hard HEAD^ # or HEAD~2, implying HEAD^^
git push -f origin master # force to push
```
