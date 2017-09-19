---
layout: post
title: Python Iterables and Generators
date: 2017-09-20 01:35:00 +0900
categories:
  - Python
tags:
  - Python 3
  - tutorial
references:
  - Fluent Python 1E, Ramalho
  - https://www.python.org/dev/peps/pep-0255
  - https://www.python.org/dev/peps/pep-0380
  - https://docs.python.org/3/library/functions.html#iter
comments: true
---

## tl;dr
This post briefly summarizes the chapter 14 of Fluent Python by Ramalho. Please check the book for more detailed examples and usages.

> Iteration is fundamental to data processing. And when scanning datasets that don’t fit in memory, we need a way to fetch the items lazily, that is, one at a time and on demand. This is what the Iterator pattern is about.

```python
import re
import reprlib
RE_WORD = re.compile(r'\w+')

class Sentence:
    def __init__(self, text):
        self.text = text
        self.words = RE_WORD.findall(text)

    def __getitem__(self, index):  # for loop is available
        return self.words[index]

    def __len__(self):  # it completes the sequence protocol
        return len(self.words)

    def __repr__(self):
        return 'Sentence(%s)' % reprlib.repr(self.text)

>>> s = Sentence('Hello there! Mighty fine morning! if you ask me, I am Waldo.')
>>> s
Sentence('Hello there!..., I am Waldo.')
>>> for word in s: print(word)
Hello
there
Mighty
...
Waldo
>>> list(s)  # list takes any iterable to build a list instance
['Hello', 'there', 'Mighty', ..., 'Waldo']
```

## What are "iterables"
How is the above `for` loop possible?

> Whenever the interpreter needs to iterate over an object `x`, it automatically calls `iter(x)`.

And the `iter` built-in function needs two methods of the object, namely `__iter__` and `__getitem__`.

1. It seeks `__iter__` first,
2. and if not found, `iter` wants to make an iterator via `__getitem__`.
3. If both are not implemented, `iter` raises `TypeError`.

Based on these observations, we can describe how the Python iterable quacks:

> Iterable: Any object from which the iter built-in function can obtain an iterator. Objects implementing an `__iter__` method returning an iterator are iterable. Sequences are always iterable; so as are objects implementing a `__getitem__` method which takes 0-based indexes.

Note also that the goose-typing is supported with `abc.Iterable`, which strictly requires `__iter__` method; it does not consider `__getitem__`.

## Python for loop and iterator
```python
>>> s = 'ABC'
>>> for c in s:
...     print(c)
```
The above code is equivalent with the below.
```python
>>> s = 'ABC'
>>> it = iter(s)
>>> while True:
...     try:
...         print(next(it))
...     except StopIteration:  # it signals the iterator exhausted
...         del it
...         break
```
> Iterator: Any object that implements the `__next__` no-argument method which returns the next item in a series or raises `StopIteration` when there are no more items. Python iterators also implement the `__iter__` method so they are iterable as well.

## A generator function and generator expressions
> Any Python function that has the `yield` keyword in its body is a generator function: a function which, when called, returns a generator object. In other words, a generator function is a generator factory.

A generator function in Python make the implementation of an iterator much easier.
```python
def fib():  # invoking it does not result in the infinite loop
    a, b = 0, 1
    while 1:
        yield b
        a, b = b, a+b

>>> fibgen = fib()  # it just gives us a generator
>>> fibgen  # used for loops
<generator object fib at 0x7efec9e86620>
```
We can make `Sentence` truly lazy via `re.finditer` (also a lazy version of `re.findall`).
```python
class Sentence:
    def __init__(self, text):
        self.text = text

    def __iter__(self):
        for match in RE_WORD.finditer(self.text):
            yield match.group()
```
Also `Sentence` gets shorter using **generator expressions**.
> A generator expression can be understood as a lazy version of a list comprehension

```python
class Sentence:
    def __iter__(self):
        return (match.group() for match in RE_WORD.finditer(self.text))
```

## Generators can "yield from" sub-generators
The below `chain` implementation tells us what the new keyword does for generators.
```python
def chain(*iters):
    for i in iters:
        yield from i

>>> list(chain('ABC', range(3)))
['A', 'B', 'C', 0, 1, 2]
```

## "iter" can even take a normal function, not a generator
See the [docs](https://docs.python.org/3/library/functions.html#iter).
