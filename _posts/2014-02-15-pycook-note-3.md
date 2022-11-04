---
layout: post
title: Python CookBook 3 Reading Note Part 2
---

### Data encoding and processing

* Decoding and encoding with hexadecimal digit. there are some difference
between the output of `base64.b16encode()` and `binascii.b2a_hex()`, the
former will only output the uppcase hexadecimal letters.

### Functions

*  The way to enable function only accept certain arguments by keywords

```python
def recv(maxsize,*,block): #just put * argument in
     pass

 recv(maxsize,True) #TypeError
 recv(maxsize,block=True) # Fine!!
```

* The way to capture variables in anonymous functions

```python
>>> x = 10
>>> a = lambda y, x=x: x + y
>>> x = 20
>>> b = lambda y, x=x: x + y
>>> a(10)
20
>>> b(10)
30
```

* The `partial()` could let python resemble haskell way of handling function

* Accessing variables defined inside a closure

```python
def sample():
    n = 0

    def set_n(value):
        nonlocal n
        n = value
```

### Object & Class

* Customizing string formatting

```python
class Date:
    def __init__(self,y,m,d):
        self.year = y
        self.month = m
        self.day = d

    def __format__(self, code):
        if code =='':
            code = 'ymd'
        fmt = _format[code]  #_format is the formats dict
        return fmt.format(d)

d = Date(2012,3,4)
print('The date is {:ymd}'.format(d))
```

* You can often greatly reduce the memory footprint of instances by adding
`__slots__` attribute to the class definition. When you define `__slots__`, Python uses a much more compact internal representation for instances. Instead of each instance consisting of a
    dictionary, instances are built around a small fixed-sized array, much
    like a tuple or list.

```python
class Date:
    __slots__ = ['year','month','day']
    def __init__(self,y,d,m):
        self.year = y
        self.month = m
        self.day = d
```

* Create and managed attributes: use "property"

```python
class Person:
    def __init__(self,first_name):
        self.first_name = first_name

    @property
    def first_name(self):
        return self._first_name

    @property.setter
    def first_name(self,value):
        if not isinstance(value,str):
            raise TypeError("Expected a string")
        self._first_name = value

    @property.deleter
    def first_name(self):
        raise AttributeError("Can't delete the attribute")
```

* Simplifying the initialization of data structure

```python
class Structure:
    _field = []
    def __init__(self, *args):
        if len(args) != len(self._field):
            raise TypeError("Expected {} arguments".format(len(self._field)))
        for name,value in zip(self._field, args):
            setattr(self,name,value)

class Stock(Structure):
    _field = ['name','shares','price']
```

* Defining an interface

```python
for abc import ABCMeta,abstractmethod

class IStrem(metaclass=ABCMeta):
    @abstractmethod
    def read(self, maxbytes=1):
        pass

    def write(self,deta):
        pass
```

* Calling a method on an object given the name as a string

```python
import operator
operator.methodcaller('distance',0,0)(obj)
```

* __weak reference__ is a pointer to an object that does not increase its reference count

* If we customized the `__del__()` function the `gc.collect()` will not work

* Creating cached instances

```python
class Spam:
    def __init__(self, name):
        self.name = name

import weakref
_spam_cache = weakref.WeakValueDictionary()

def get_spam(name):
    if name not in _spam_cache:
        s = Spam(name)
        _spam_cache[name] = s
    else:
        s = _spam_cache[name]
    return s
```

### Metaprogramming

* The `warps(func)` helps to preserve the meta infomation such as `__name__,__doc__` etc. about the warpped function

```python
import time
from functools import warps

def timethis(func):
    @warps(func)
    def warpper(*args,**kargs):
        start = time.start()
        result = func(*args, **kargs)
        end = time.end()
        print(func.__name__,end - start)
        return result
    return warpper

@timethis
def countdown(n):
    while n > 0:
        n -= 1
```

* unwarpping a decorator

```python
@somedeco
def add(x,y):
    return x + y

orig_add = add.__warpped__
```

however it only work if the implementation of a decorator copies metadata
using `@warps` from the `functools` module or sets the `__warpped__` attribute

* Defining a decorator that takes argument

```python
from functools import warps
import logging

def logged(level, name=None, message=None):
    def decorate(func):
        logname = name if name else func.__module__
        log = logging.getLogger(logname)
        logmsg = message if message else func.__name__

        @warps(func)
        def warpper(func):
            log.log(level,logmsg)
            return func(*args, **kargs)
        return warpper
    return decorate
```

* Using a metaclass to control instance creation

```python
class NoInstances(type): # should inhertant from type
    def __call__(self, *args, **kwargs):
        raise TypeError("Can't instantiate directory")

class spam(metaclass=NoInstances):
    @staticmethod
    def grok(x):
        print('Spam.grok')


class Singleton(type):
    def __init__(self, *args, **kwargs):
        self.__instance = None
        super().__init__(*args,**kwargs)

    def __call__(self, *args, **kwargs):
        if self.__instance = None:
            self.__instance = super().__call__(*args,**kwargs)
            return self.__instance
        else:
            return self.__instance

class Spam(metaclass=Singleton):
    def __init__(self):
        print('Creating spam')
```

* Property factory

```python
def typed_property(name, expected_type):
    storage_name = '_' + name

    @property
    def prop(self):
        return getattr(self, storage_name)

    @prop.setter
    def prop(self,value):
        if not isinstance(value, expected_type):
            raise TypeError("unmatched type")

        setattr(self,storage_name,value)

    return prop
```

- Defining context managers

```python
import time
from contextlib import contextmanager

@contextmanager
def timethis(label):
    start = time.time()
    try:
        yield

    finally:
        end = time.time()
        print('{}: {}'.format(label, end- start)


with timethis('counting'):
    n = 100000
    while n > 0:
        n -= 1

@contextmanager
def list_transaction(orig_list):
    working = list(orig_list)
    yield working
    orig_list[:] = working

with list_transaction(items) as w:
    ......
```

* The `dis` module can be used to output a disassembly of any python function
