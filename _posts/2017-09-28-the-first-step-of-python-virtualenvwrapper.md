---
layout: post
title: The First Step of Python Virtualenvwrapper for Ubuntu
date: 2017-09-28 21:20:00 +0900
categories:
  - Python
tags:
  - virtualenv
  - tutorial
references:
  - http://virtualenvwrapper.readthedocs.io/en/stable
  - https://virtualenv.pypa.io/en/stable
comments: true
---

## tl;dr

<script src="//gist.github.com/dgkim5360/1219c72793d940b1a3c7e9a68d6b621f.js"></script>

Managing Python virtual environments gets easier via `virtualenvwrapper` which delegates `virtualenv`. This post shows basic usages of Python virtual environments using `virtualenvwrapper`, especially for Ubuntu (and maybe Debian). It does not introduce the way of installation from PyPI, but from the Ubuntu package repositories. Such installation makes using `virtualenv` more convenient to connect each of Python 2 and 3, which Ubuntu contains by default, without confusion.

By default, `virtualenvwrapper` piles my `virtualenv`s in the path `$HOME/.virtualenvs`, in which various customizing scripts stay. The `virtualenvwrapper` docs is worth reading.
