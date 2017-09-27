---
layout: post
title: Basics of Python Coroutines
date: 2017-09-27 19:44:00 +0900
categories:
  - Python
tags:
  - Python 3
  - tutorial
references:
  - Fluent Python 1E, Ramalho
  - https://www.python.org/dev/peps/pep-0342
  - https://www.python.org/dev/peps/pep-0380
  - https://docs.python.org/3/whatsnew/3.3.html#pep-380  
  - http://www.dabeaz.com/coroutines/Coroutines.pdf
comments: true
---

## tl;dr
This post mainly summarizes the chapter 16 of Fluent Python by Ramalho. Please check the book for more detailed examples and usages.

> A coroutine is syntactically like a generator.

Here is an example of the averager, once implemented via [the closure](/blog/python/2017/09/closures-and-decorators-in-python/).

```python
def co_averager():
    total = 0.0
    count = 0
    average = None
    while True:
        term = yield average
        total += term
        count += 1
        average = total/count

>>> averager = co_averager()
>>> next(averager)  # priming a coroutine
>>> averager.send(5)
5.0
>>> averager.send(6)
5.5
>>> averager.send(19)
10.0
```

## Basic workflow of a coroutine
Prior to the above `co_averager`, the simpler example is introduced here to illustrate the basic workflow of Python coroutine. Check the status of a coroutine and what it does via `yield` and `send`.
`simple_coro2`
```python
def co_example(a):
    print('-> started: a =', a)
    b = yield a
    print('-> received: b =', b)
    c = yield a + b
    print('-> received: c =', c)

>>> from inspect import getgeneratorstate
>>> coro = co_example(10)
>>> getgeneratorstate(coro)
'GEN_CREATED'
>>> coro
<generator object co_example at 0x7f6165564bf8>
>>> next(coro)  # priming
-> started: a = 10
10
>>> getgeneratorstate(coro)
'GEN_SUSPENDED'
>>> coro.send(15)
-> received: b = 15
25  # a + b
>>> coro.send(20)
-> received: c = 20
StopIteration: ...  # Exception thrown
>>> getgeneratorstate(coro)
'GEN_CLOSED'
```

The `GEN_CREATED` status implies that the coroutine has not started and therefore it does not exploit `send` yet; we have to move on the `next` step to advance the coroutine to the first `yield`. This action is called "priming" a coroutine. The below decorator for priming coroutine is convenient sometimes.

```python
from functools import wraps

def coroutine(func):
    """Primes `func` by advancing to the first `yield`"""
    @wraps(func)
    def primer(*args, **kwargs):
        gen = func(*args, **kwargs)
        next(gen)
        return gen
    return primer
```

## Handling exceptions and termination in coroutines
A coroutine is closed when it raises an exception which is not handled by itself.
```python
>>> averager = coro_averager()
>>> averager.send(100)  # possible if it is @corotine decorated
100.0
>>> averager.send('awkward hello')
TypeError: ...
>>> getgeneratorstatus(averager)
'GEN_CLOSED'
>>> averager.send(50)
StopIteration: ...
```
To control their actions, the caller can `throw` and `close` directly to coroutines. The handled exceptions do not stop the coroutine. And if the caller `close`s the coroutine, it silently stops and has `GEN_CLOSED` state.
```python
@coroutine
def coro_exc_handle():
    print('-> coroutine started')
    while True:
        try:
            x = yield
        except:
            print('TypeError handled, and continues to work...')
        else:
            print('-> coroutine received: {!r}'.format(x))

>>> coro = coro_exc_handle()
-> coroutine started
>>> coro.send(7)
-> coroutine received: 7
>>> coro.throw(TypeError)
TypeError handled, and continues to work...
>>> getgeneratorstate(coro)
'GEN_SUSPENDED'
>>> coro.send(7)
-> coroutine received: 7
>>> coro.throw(NameError)
NameError: ...  # unhandled exception causes the coroutine to stop
>>> getgeneratorstate(coro)
'GEN_CLOSED'
```

## Coroutines can return via the exception
We visit the `averager` again, but in this stage it does not yield every single incremental average but returns the final average. From Python 3.4+ it is possible for coroutines to be able to return.

```python
from collections import namedtuple

Result = namedtuple('Result', 'count average')

@coroutine
def coro_averager_return():
    total = 0.0
    count = 0
    while True:
        term = yield  # it yields nothing
        if term is None:
            break
        total += term
        count += 1
    return Result(count, total/count)

>>> averager = averager_returns()
>>> averager.send(10)
>>> averager.send(20)
>>> averager.send(None)  # stop it on purpose
StopIteration: Result(count=2, average=15.0)
```
Unfortunately its `Result` is delivered via `StopIteration` exception. We may get the `Result` by manually catching the exception value.
```python
>>> try:
...     averager.send(None)
... except StopIteration as exc:
...     result = exc.value
...
>>> result
Result(count=2, average=15.0)
```
> This roundabout way of getting the return value from a coroutine makes more sense when we realize it was defined as part of PEP 380, and the yield from construct handles it automatically by catching `StopIteration` internally. This is analogous to the use of `StopIteration` in for loops: the exception is handled by the loop machinery in a way that is transparent to the user. In the case of yield from , the interpreter not only consumes the `StopIteration`, but its value attribute becomes the value of the yield from expression itself.

## The brand-new "yield from"
This Python keyword (available in Python 3.3+) pipes the generators and coroutines in a smooth manner.

> When a generator `gen` calls `yield from` `subgen()`, the `subgen` takes over and will yield values to the caller of `gen`; the caller will in effect drive `subgen` directly. Meanwhile `gen` will be blocked, waiting until `subgen` terminates.

For an intuitive example, we revisit [the usage of Python generator using `yield from`](/blog/python/2017/09/closures-and-decorators-in-python/).
```python
def chain(*iters):
    for i in iters:
        yield from i

>>> list(chain('ABC', range(3)))
['A', 'B', 'C', 0, 1, 2]
```
> The first thing the `yield from x` expression does with the `x` object is to call `iter(x)` to obtain an iterator from it. This means that `x` can be any iterable.

> The main feature of `yield from` is to open a bidirectional channel from the outermost caller to the innermost subgenerator, so that values can be sent and yielded back and forth directly from them, and exceptions can be thrown all the way in without adding a lot of exception handling boilerplate code in the intermediate coroutines.

You can see how the behaviors of subgenerators propagate through `yield from` pipes. I left the example from the book and this is worth chewing. Note that the result of `yield from` is what the coroutine returns but we do not have to manage any exception handling. `yield from`

```python
from collections import namedtuple
Result = namedtuple('Result', 'count average')

# the subgenerator
def averager():
    total = 0.0
    count = 0
    average = None
    while True:
        term = yield
        if term is None:
            break
        total += term
        count += 1
        average = total/count
    return Result(count, average)

# the delegating generator
def grouper(results, key):
    while True:
        results[key] = yield from averager()

# the client code, a.k.a. the caller
def main(data):
    results = {}
    for key, values in data.items():
        group = grouper(results, key)
        next(group)  # prime it
        for value in values:
            group.send(value)
        group.send(None)  # important!
    # print(results)  # uncomment to debug
    report(results)


# output report
def report(results):
    for key, result in sorted(results.items()):
        group, unit = key.split(';')
        print('{:2} {:5} averaging {:.2f}{}'.format(
        result.count, group, result.average, unit))

data = {
    'girls;kg':
        [40.9, 38.5, 44.3, 42.2, 45.2, 41.7, 44.5, 38.0, 40.6, 44.5],
    'girls;m':
        [1.6, 1.51, 1.4, 1.3, 1.41, 1.39, 1.33, 1.46, 1.45, 1.43],
    'boys;kg':
        [39.0, 40.8, 43.2, 40.8, 43.1, 38.6, 41.4, 40.6, 36.3],
    'boys;m':
        [1.38, 1.5, 1.32, 1.25, 1.37, 1.48, 1.25, 1.49, 1.46],
}

if __name__ == '__main__':
    main(data)
```

```bash
$ python3 coroaverager3.py
9 boys averaging 40.42kg
9 boys averaging 1.39m
10 girls averaging 42.04kg
10 girls averaging 1.43m
```
Here is the end of the summary. Much more comprehensive examples can be found in the references. The taxi simulation in Fluent Python is easy to follow and DaBeaz includes comprehensive examples dealing with coroutines but no help of `yield from` (slides made in 2009). We actually see that "delegating subgenerators" gets complicated without the power of `yield from`.
