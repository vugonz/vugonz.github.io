---
date: 2024-12-12T20:19:00Z
author: "Gonçalo"
title: "Singletons in Python"
description: Using Python modules for singletons!
tldr: Using Python modules to implement the singleton pattern and caveats 
draft: true
toc: true
---

## Intro
Singletons get a bad rep, I don't really know why, I won't pretend to understand much, but I am single 🔓❤️ so I should at least know how singletons work right? (cringe)
## Modules as Singletons
My favourite way of implementing the singleton pattern in Python is through 💫 *modules* 💫.

That's because Python modules are inherently singletons! They are loaded only once, and their reference remains unchanged throughout the program. So, a super simple way to implement singleton behavior is to use a module!

```py
# singleton.py
__a = 0

def get_value():
    return __a

def set_value(n: int):
    global __a
    __a = n
```
Any other file importing this module will have access to `__a`'s value and changing it will be reflected everywhere else, regardless of how many times you call `import singleton`.

Simple usage of the singleton in any file:
```python
import singleton

singleton.set_value(1)
singleton.get_value()
```

## Caveats
Reloading the module with something like `importlib.reload` (which is unlikely to happen accidentally) will break the singleton principle, as the module's code will be executed again.
However, there are more subtle ways to unintentionally break the principle. If you somehow dabble with `sys.path` you might accidently lead Python to load the same module twice! It's not uncommon to toy around with the path, but this can introduce some nasty and hard to find bugs in your program!

Say you have the following structure:
```
foo/
    bar/
        singleton/
            __init__.py
            singleton.py
        __init__.py
        bar.py
    __init__.py
    foo.py
```

```python
# foo.py
from bar.singleton import singleton

singleton.set_value(1)
print(singleton.get_value())

from bar import bar # this will cause bar.py to be executed
```
```python
# bar.py
import os

os.sys.path += [os.path.dirname(__file__)] # not uncommon to toy with sys.path

from singleton import singleton

print(singleton.get_value())
```

Now if you run it with `python foo.py` the output will be `1 0`. Here's the rundown:
- `foo.py`
    - imports singleton
    - sets value to 1
    - reads value -> 1
    - causes python to execute `bar.py` 
- `bar.py`
    - imports singleton
    - reads value -> 0

This is incorrect behavior, both should be reading the same value from the singleton. 
Let's inspect what is happening.
```repl
>>> import foo
1
0
>>> import os
>>> os.sys.modules.keys()
dict_keys(..., 'bar', 'bar.singleton', 'bar.singleton.singleton', 'singleton', 'singleton.singleton', 'bar.bar', 'foo'])
```
So it looks like we have modules that were loaded twice! 
Taking a closer look at those will let us conclude if this suspicion is correct.
```repl
>>> os.sys.modules['singleton.singleton']
<module 'singleton.singleton' from '/home/vugonz/blog/singletons/foo/bar/singleton/singleton.py'>
>>> os.sys.modules['bar.singleton.singleton']
<module 'bar.singleton.singleton' from '/home/vugonz/blog/singletons/foo/bar/singleton/singleton.py'>
```
Yep, we loaded the same module twice...
What exaclty is happening? There is an [amazing video](https://www.youtube.com/watch?v=QCSz0j8tGmI) that goes in-depth on how Python modules work, in it I am sure everything will be made clearer.

This is a super construded example, but messing with sys.path is not uncommon in Python, I do it ocassionaly and had no idea of some of its side effects.

## Honorable Mentions 
As an honorable mention, and because of its cool name, I'll also mention the *Borg* pattern. It is in everything similar to the classical singleton approach, but instead of keeping the same object instance, it shares the state among all instances. This means that while each instance may be a distinct object, they all share the same internal state.
```python
>>> class Borg:
...     __shared_state = {}
...     def __init__(self):
...             self.__dict__ = self.__shared_state
... 
>>> inst1 = Borg()
>>> inst1.a = 1
>>> inst2 = Borg()
>>> inst2.a
1
>>> inst1 is inst2
False
```
In a convetional singleton approach `inst1 is inst2` would be True, but not for *Borg*. 😎
