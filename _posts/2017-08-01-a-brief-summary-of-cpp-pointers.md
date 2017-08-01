---
layout: post
title: a brief summary of C++ pointers
date: 2017-08-01 22:03:00 +0900
categories:
  - C++
tags:
  - tutorial
references:
  - http://www.cplusplus.com/doc/tutorial/pointers/
comments: true
---

## tl;dr

This post summarizes the reference document regarding C++ pointers. For more detailed introduction with comprehensive images, see the reference.

## operators dealing with memory addresses
```c++
/* address-of operator "&" */
int bar = 42;
foo = &bar  // foo contains the address of bar

/* dereference operator "*" */
baz = *foo  // baz contains the value of bar, to which is pointed by foo
baz == 42  // thus it's true
```

## declaration syntax of pointers
```c++
/* caution: this syntax is not related to dereference operator at all */
int bar = 42;  // declare an int variable
int * foo = &bar  // declare a pointer of int data type

/* also be careful with declaring pointers */
int * p1, p2;  // p2 is not declared as a pointer
int * p1, * p2;  // both are pointers
```

## pointers and arrays
```c++
int arr[5];  // arr refers to the address of its first element (already allocated)
int * p_arr;

p_arr = arr;  // assign p_arr with the address of arr, it's valid
arr = p_arr;  // invalid, arr cannot have another address
```

## pointer arithmetics: it depends on sizeof(data type)
```c++
char * p_char;  // say p_char = 1000
short * p_short;  // p_short = 2000
long * p_long;  // p_long = 3000

++p_char;  // p_char = 1001
++p_short;  // p_short = 2002
++p_long;  // p_long = 3004

*(p++);  // increment pointer, and dereference unincremented address
*(++p);  // increment pointer, and dereference incremented address
++(*p);  // dereference pointer, and increment the value it points to
(*p)++;  // dereference pointer, and post-increment the value it points to
```

## void pointers, null pointers
```c++
/* void pointers are not limited to data type */
#include <iostream>
using namespace std;

void increase (void* data, int psize) {
  if ( psize == sizeof(char) )
  { char* pchar; pchar=(char*)data; ++(*pchar); }
  else if (psize == sizeof(int) )
  { int* pint; pint=(int*)data; ++(*pint); }
}

int main () {
  char a = 'x';
  int b = 1602;
  increase (&a,sizeof(a));
  increase (&b,sizeof(b));
  cout << a << ", " << b << '\n';
  return 0;
}  // output: y, 1603

/* null pointers is literally nothing */
int * p = nullptr, * q = 0;  // both are valid null pointers
```

## points to functions
```c++
#include <iostream>
using namespace std;

int addition (int a, int b) { return (a+b); }
int subtraction (int a, int b) { return (a-b); }
int operation (int x, int y, int (*functocall)(int,int)) {
  int g;
  g = (*functocall)(x,y);
  return (g);
}

int main () {
  int m,n;
  int (*minus)(int,int) = subtraction;

  m = operation (7, 5, addition);
  n = operation (20, m, minus);
  cout <<n;
  return 0;
}
```
