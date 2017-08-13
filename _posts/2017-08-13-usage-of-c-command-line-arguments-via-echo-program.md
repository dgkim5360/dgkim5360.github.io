---
layout: post
title: usage of C command-line arguments via echo program
date: 2017-08-13 14:45:00 +0900
categories:
  - C
tags:
  - tutorial
references:
  - The C Programming Language 2E, Kernighan & Ritchie
comments: true
---

## tl;dr

> When `main` is called, it is called with two arguments. The first (conventionally called `argc`, for argument count) is the number of command-line arguments the program was invoked with; the second (`argv`, for argument vector) is a pointer to an array of character strings that contain the arguments, one per string.

Below is an example code of `echo` program.

```c
#include <stdio.h>

int main(int argc, char *argv[]) {
  while (--argc > 0)
    printf((argc > 1) ? "%s " : "%s", *(++argv));
  printf("\n");
  return 0;
}
```

## argc and argv

`argc` is an integer representing the number of arguments, and `argv` is a pointer to an `char` array, i.e., a string array, which stores the argument strings. `argv` contains at least one string, which is the command itself. For example, from the echo command

```bash
$ echo hello, world
```

`argv` would contain items like `{"echo", "hello,", "world"}`. Therefore  `argc >= 1`, and `argc == 1` implies the program gets no arguments.

Also note that `argv` has additional null pointer such that `argv[argc] == 0`.


## pointer to a string array, argv

Since `argv` is a pointer, assuming it contains items `{"echo", "hello,", "world"}`, the following statements are valid.

* `*argv == argv[0] == "echo"`
* `*++argv == *(++argv) == argv[1] == "hello,"`
* `(*argv++)[0] == argv[1][0] == 'e'`, after the previous increment
* `*argv == argv[2] == "world"`, after the previous increment
* `*++argv == argv[3] == argv[argc] == 0 // or null pointer`, after the previous increment
