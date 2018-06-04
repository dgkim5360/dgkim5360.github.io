---
layout: post
title: The First Step for Python Asyncio with Coroutines
date: 2017-10-24 23:30:00 +0900
categories:
  - Python
tags:
  - Python3
  - tutorial
references:
  - Fluent Python 1E, Ramalho
  - https://www.python.org/dev/peps/pep-3153
  - https://www.python.org/dev/peps/pep-3156
  - https://www.python.org/dev/peps/pep-0492
  - https://www.python.org/dev/peps/pep-0525
  - https://www.python.org/dev/peps/pep-0530
  - https://docs.python.org/3/library/asyncio.html
  - http://www.dabeaz.com/coroutines/Coroutines.pdf
  - https://stackoverflow.com/questions/40571786/asyncio-coroutine-vs-async-def
  - https://snarky.ca/how-the-heck-does-async-await-work-in-python-3-5
---

## tl;dr
Let's first look at the spinner example from the book *Fluent Python*, chapter 18. The below Python program runs a loading spinner while it simulates a time-consuming job.

```python
import asyncio
import itertools
import sys


@asyncio.coroutine
def spin(message):
    write, flush = sys.stdout.write, sys.stdout.flush
    for char in itertools.cycle('|/-\\'):
        status = char + ' ' + message
	leng = len(status)
	write(status)
	flush()
	write('\x08' * leng)
	try:
	    yield from asyncio.sleep(.1)
	except asyncio.CancelledError:
	    break
    write(' ' * leng + '\x08' * leng)


@asyncio.coroutine
def heavy_work():
    # pretend waiting a long time for I/O
    yield from asyncio.sleep(5)
    return 42


@asyncio.coroutine
def supervisor():
    spinner = asyncio.async(spin('thinking ...'))
    print('spinner object: ', spinner)
    result = yield from heavy_work()
    spinner.cancel()
    return result


def main():
    loop = asyncio.get_event_loop()
    result = loop.run_until_complete(supervisor())
    loop.close()
    print('answer: ', result)


if __name__ == '__main__':
    main()
```

The `asyncio` constructs concurrency with coroutines driven by an **event loop**.

Please note that the latest version of Python when the book has been written was 3.4, when `async` and `await` keywords were not there yet. Here goes 3.5 version of the spinner example.

```python
async def spin(message):
    write, flush = sys.stdout.write, sys.stdout.flush
    for char in itertools.cycle('|/-\\'):
        status = char + ' ' + message
	leng = len(status)
	write(status)
	flush()
	write('\x08' * leng)
	try:
	    await asyncio.sleep(.1)
	except asyncio.CancelledError:
	    break
    write(' ' * leng + '\x08' * leng)


async def heavy_work():
    # pretend waiting a long time for I/O
    await asyncio.sleep(5)
    return 42


async def supervisor():
    spinner = asyncio.ensure_future(spin('thinking ...'))
    print('spinner object: ', spinner)
    resut = await heavy_work()
    spinner.cancel()
    return result


if __name__ == '__main__':
    main()  # main is the same
```

## Introduction
Before `asyncio` arrived, there was not a strict distinction between a generator and a coroutine. Sometimes one could tell "this is a coroutine" because it contains the `= yield` syntax (implying coroutines are for data pipe-lining), but it does not apply always.

A traditional coroutine focuses to deal with data as its consumer sends data (or exceptions) to it. It naturally controls its states via `= yield` syntax, e.g., it runs until it hits `yield` and waits for a consumer to `send` data. In other words, it acts in a concurrent manner. 

With the advent of Python `asyncio` as standard library, [PEP 3156](https://www.python.org/dev/peps/pep-3156/#coroutines) made the distinction of `@asyncio.coroutine`-decorated coroutine for the suitable use of coroutines with `asyncio`. And this coroutine is called **generator-based coroutine** after the invention of **native coroutines**.

As coroutines have done crucial roles in asynchronous works for Python, Python 3.5 introduced **native coroutines** with brand-new `async` and `await` keywords, to make the significant difference of coroutines, especially with `asyncio`.

So, as the new keywords imply, native coroutines seem to take more weights on this waiting behavior for asynchronous handling in a single thread.

## Traditional vs. native coroutines

According to [PEP 492](https://www.python.org/dev/peps/pep-0492),

> The following syntax is used to declare a *native coroutine*:
> ```python
> async def read_data(db):
>     pass
> ```
>
> Key properties of coroutines:
> * `async def` functions are always coroutines, even if they do not contain await expressions.
> * It is a `SyntaxError` to have `yield` or `yield from` expressions in an `async` function.
> * Internally, two new code object flags were introduced:
>   * `CO_COROUTINE` is used to mark *native coroutines* (defined with new syntax).
>   * `CO_ITERABLE_COROUTINE` is used to make *generator-based coroutines* compatible with *native coroutines* (set by `types.coroutine() function).
> * Regular generators, when called, return a *generator object*; similarly, coroutines return a *coroutine object*.
> * `StopIteration` exceptions are not propagated out of coroutines, and are replaced with `RuntimeError`. For regular generators such behavior requires a future import (see PEP 479).
> * When a *native coroutine* is garbage collected, a `RuntimeWarning` is raised if it was never awaited on.
>
> The behavior of existing *generator-based coroutines* in `asyncio` remains unchanged.
>
> Differences of native coroutines from generators:
> 1. Native coroutine objects do not implement `__iter__` and `__next__` methods. Therefore, they cannot be iterated over or passed to `iter()`, `list()`, `tuple()` and other built-ins. They also cannot be used in a `for..in` loop.
> An attempt to use `__iter__` or `__next__` on a native coroutine object will result in a `TypeError`.
> 2. Plain generators cannot `yield from` native coroutines: doing so will result in a `TypeError`.
> 3. Generator-based coroutines (for `asyncio` code must be decorated with `@asyncio.coroutine`) can `yield from` native coroutine objects.
> 4. `inspect.isgenerator()` and `inspect.isgeneratorfunction()` return `False` for native coroutine objects and native coroutine functions.

## The event loop of asyncio

The spinner example shows the basics: How `asyncio` starts an event loop, drives coroutines (or tasks) asynchronously, and then gets the results at the proper moment.

> The event loop is the central execution device provided by asyncio. It provides multiple facilities, including:
> * Registering, executing and cancelling delayed calls (timeouts).
> * Creating client and server transports for various kinds of communication.
> * Launching subprocesses and the associated transports for communication with an external program.
> * Delegating costly function calls to a pool of threads.

`asyncio` has more and much more than the spinner. Check the references including great slides and essays.
