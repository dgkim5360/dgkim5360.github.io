---
layout: post
title: duck typing vs. goose typing, Pythonic interfaces
date: 2017-07-18 00:20:00 +0900
categories:
  - python
tags:
  - python 3
  - interface
  - tutorial
references:
  - https://en.wikipedia.org/wiki/Duck_typing
  - https://docs.python.org/3/glossary.html#term-duck-typing
  - https://docs.python.org/3/reference/datamodel.html#special-method-names
  - https://docs.python.org/3/glossary.html#term-abstract-base-class
comments: true
---

## tl;dr

This post summarizes the Pythonic concept of interface, mainly by taking notes from the chapter 11 of the book "Fluent Python".

## duck typing and Python protocols

> In the context of Object Oriented Programming, a protocol is an informal interface, defined only in documentation and not in code. For example, the sequence protocol in Python entails just the `__len__` and `__getitem__` methods. Any class `Spam` that implements those methods with the standard signature and semantics can be used anywhere a sequence is expected. Whether `Spam` is a subclass of this or that is irrelevant, all that matters is that it provides the necessary methods.

The following example shows how the sequence protocol works. `SequenceLikeClass` implements the sequence protocol by duck typing: to equip with special methods `__len__` and `__getitem__`, which are methods corresponding to the sequence protocol.

```python
class SequenceLikeClass:
    def __init__(self, items):
        self.sequence = list(items)
    def __len__(self):
        return len(self.sequence)
    def __getitem__(self, position):
        return self.sequence[position]
```

Although it does not inherit from sequence class like list, it behaves well like some sequence class.

```python
# instantiate
>>> seq = SequenceLikeClass(range(5))
# len works
>>> len(seq)
5
# getting an item through index
>>> seq[2]
2
# negative index supported
>>> seq[-1]
4
# slicing works
>>> seq[2:4]
[2, 3]

# for loop works even if it does not have __iter__ method
>>> for j in seq: print(j)
0
1
2
3
4

# in operator works even if it does not have __contains__
>>> 1 in seq
True

# choice requires sequence as an argument, it naturally works
>>> from random import choice
>>> choice(seq)
1 # randomly chosen among 0, 1, 2, 3, and 4
```

## goose typing and ABCs (abstract base classes)

ABCs makes protocols explicit.

> Abstract base classes complement duck-typing by providing a way to define interfaces when other techniques like hasattr() would be clumsy or subtly wrong (for example with magic methods). ABCs introduce virtual subclasses, which are classes that don’t inherit from a class but are still recognized by isinstance() and issubclass()

> What goose typing means is: `isinstance(obj, cls)` is now just fine... as long as `cls` is an Abstract Base Class.

```python
from collections.abc import Sized  # Sized needs __len__
class SizedClass(Sized):
    def __init__(self):
        print('hello')

>>> sc = SizedClass()  # without __len__, Python gives us an error when instantiating, not importing
TypeError: Can't instantiate abstract class SizedClass with abstract methods __len__


class RealSizedClass(Sized):  # let's redefine
    def __len__(self):
        return 2

>>> rsc = RealSizedClass()
>>> isinstance(rsc, Sized)
True  # of course

@Sized.register  # register decorator allows us to do goose typing
class BearSizedClass:  # merely an object with __len__ method
    def __len__(self):
        return 2

>>> bsc = BearSizedClass()
>>> isinstance(bsc, Sized)
True  # actually, it is also true without the decorator...
# why? check the book. (abc.Sized has __subclasshook__ implemented)
```
