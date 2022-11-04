---
layout: post
title: Python CookBook 3 Reading Note Part 2
---

### Strings and text

* Elegent way to change date format

```python
datepat = re.compile(r'(\d+)/(\d+)/(\d+)')
for month, day, year in datepat.findall(text):
    print('{}-{}-{}'.format(year,month,day)

#another way
datepat.sub(r'\3-\2-\1)',text)
```

* Aligning text strings

```python
text.ljust(20) # 20 char width, text in the left
text.rjust(20, '=') # 20 char width, text in the right, '=' fill blank
```

* Reforming text

```python
s ="I am the bond of my sword, steel is my body and fire is \
    my blood... Unknown to death, nor known to live....\
    unlimited blade work!!!"

import textwarp

print(textwarp.fill(s,40)) # adjust text width to 40 char
```

### Iterators techniques

* DFS

```python
class Node:
def __init__(self, value):
    self._value = value
    self._childern = []

def __repr__(self):
    return 'Node({!r})',format(self._value)

def add_child(self,node):
    self._childern.append(node)

def __iter__(self):
    return iter(self._childern)

def depth_first(self):
    yield self
    for c in self:
        yield from c.depth_first() #elegent way of using yield from
```

* Iterating over the index-value pairs of a sequence

```python
my_list = ['a','b','c']
for idx,val in enumerate(my_list,1): # start with "1"
    print(idx,val)
```

* Flattening a nested sequence

```python
from collections import Iterable

def flatten(items, ignore_types=(str,bytes)):
    for x in items:
    if isinstance(x,Iterable) and not in isinstance(x, ignore_types):
        yield from flatten(x) # another usage of yield from
    else:
        yield x
```

### Files I/O

*  Adding or changing the encoding of an already open file

```python
import io
u = open("text.txt","rw")
f = io.TextIOWarpper(u, encoding='utf-8')
```