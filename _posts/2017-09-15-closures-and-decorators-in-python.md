---
layout: post
title: Closures and Decorators in Python
date: 2017-09-15 18:05:00 +0900
categories:
  - Python
tags:
  - Python 3
  - tutorial
references:
  - Fluent Python 1E, Ramalho
comments: true
---

## tl;dr
This post is a partial summary for the chapter 7 of Fluent Python by Ramalho. More advanced examples and detailed usages are present on the book.

> A decorator is a callable that takes another function as argument.

```python
@deco
def target():
    # ...
```
The above is basically the same as
```python
def target():
    # ...
target = deco(target)
```

## closures in Python
> Actually, a closure is function with an extended scope that encompasses non-global variables referenced in the body of the function but not defined there. It does not matter whether the function is a anonymous or not, what matters is that it can access non-global variables that are defined outside of its body.

```python
def make_averager():
    """
    >>> avg = make_averager()
    >>> avg(10)
    10.0
    >>> avg(15)
    12.5
    >>> avg(20)
    15.0
    """
    series = []
    def averager(new_value):
        series.append(new_value)
        return sum(series)/len(series)
    return averager
```

`avg` is a closure (for `averager`) because it reaches the list `series` outside the body of `averager` but the `series` is not global -- actually it is defined as a local variable of the function `make_averager`. You can inspect the object throught `__code__` and `__closure__` attributes.

```python
>>> avg.__code__.co_varnames
('new_value',)
>>> avg.__code__.co_freevars
('series',)
>>> avg.__closure__
(<cell at 0x107a44f78: list object at 0x107a91a48>,)
>>> avg.__closure__[0].cell_contents
[10, 15, 20]
```

I would just like to leave a use of the `nonlocal` declaration as an example below. The code itself tells us much of what the `nonlocal` does.

```python
def make_averager():  # improved in efficiency
    count = 0
    total = 0
    def averager(new_value):
        nonlocal count, total  # without this, it does not work
        count += 1
        total += new_value
        return total/count
    return averager
```

## a decorator is just a syntactic sugar

Let's start with the simplest.
```python
def deco(func):
    def inner():
        print('a message from inner of deco')
    return inner

@deco
def target():
    print('target called')

>>> target()
a message from inner of deco
```
It is notable that the decorated `target` refers the `inner` function, not `target` itself, i.e., the decorator `deco` replaces the decorated one. Check the below.
```python
>>> target
<function deco.<locals>.inner at 0x10063b598>
```

## decorators are executed at import time

> A key feature of decorators is that they run right after the decorated function is defined. That is usually at **import time**, i.e., when a module is loaded by Python.

```python
# registration.py
registry = []

def register(func):
    print('registering %s' % func)
    registry.append(func)
    return func

@register
def f1():
    print('hello from f1')

@register
def f2():
    print('hi from f2')

def f3():
    print('no greeting by f3')

# executor.py
from registration import registry, f1, f2, f3

def main():
    print('main function starting ...')
    print('registry: ', registry)
    f1()
    f2()
    f3()

if __name__ == '__main__':
    main()
```

Now let's see what happened when the `executor.py` is executed.

```bash
$ python3 executor.py
registering <function f1 at 0x7f40174bb668>
registering <function f2 at 0x7f40174bb6e0>
main function starting ...
('registry: ', [<function f1 at 0x7f40174bb668>, <function f2 at 0x7f40174bb6e0>])
hello from f1
hi from f2
no greeting by f3
```

The registering behavior occurs before the `main` function is called. This example shows **import time vs. run time** in that the decorator runs when the decorated functions are imported, but the decorated functions run as they are explicitly called.

## a little bit serious example: a time-checking decorator
Below is the code for the `clock` decorator which measures the elapsed time and kindly prints the result of the decorated function. Also the built-in `functools.wraps` decorator is worth learning; go search it =]

```python
import time
import functools

def clock(func):
    @functools.wraps(func)
    def clocked(*args, **kwargs):
        t0 = time.time()
        result = func(*args, **kwargs)
        elapsed = time.time() - t0
        arg_list = []
        if args:
            arg_list.append(', '.join(repr(arg) for arg in args))
        if kwargs:
            pairs = ['%s=%r'%(k, w) for k, w in sorted(kwargs.items())]
            arg_list.append(', '.join(pairs))
        arg_str = ', '.join(arg_list)
        print('[%0.8f] %s(%s) -> %r' % (elapsed, func.__name__, arg_str, result))
        return result
    return clocked
```

Finally the usage of the `clock` decorator comes below.
```python
import time

@clock
def snooze(seconds):
    time.sleep(seconds)

@clock
def factorial(n):
    return 1 if n < 2 else n*factorial(n-1)

if __name__ == '__main__':
    print('*' * 40, 'Calling snooze(.123)')
    snooze(.123)
    print('*' * 40, 'Calling factorial(6)')
    factorial(6)
```

```bash
$ python3 clock_demo.py
**************************************** Calling snooze(123)
[0.12405610s] snooze(.123) -> None
**************************************** Calling factorial(6)
[0.00000191s] factorial(1) -> 1
[0.00004911s] factorial(2) -> 2
[0.00008488s] factorial(3) -> 6
[0.00013208s] factorial(4) -> 24
[0.00019193s] factorial(5) -> 120
[0.00026107s] factorial(6) -> 720
```
