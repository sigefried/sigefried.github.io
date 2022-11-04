---
layout: post
title: Python CookBook 3 Reading Note Part 1
---

### Data Structures

* Python "start expressions" can be used to unpack elements from iterable elements.

```python
def drop_first_last(grades):
    first, *middle, last = grades            
    return middle
```

* Defaultdict will automatically create a dictionary entry for keys accessed late on. But it can be changed by using setdefault()

```python
d = {}
d.setdefault('a',[]).append(1)
```

* An Ordered Dict internally maintains a __double linked__ list that orders the keys according to the insertion order

* it is often useful to invert the keys and values of the dictionary using zip().

```python
 prices = { 'A' : 23, 'B' : 24, 'C' : 25 }
 min_price = min(zip(prices.values(),prices.keys())
```

* Just simply performing the common set operations using the keys() and items() method could find commonalities between two dictionaries.

```python
a.keys() & b.keys()
a.items() - b.items()
```

* A useful induction expression: `val = item if key is None else key(item)`

* Slice obj can be used to extract a sepcific part from List

```python
record = '...123......'        
SLICE = slice(3,5)
record[SLICE]
```

* Determining the Most Frequently reoccuriring Items in the Sequence.

```python
#words is a word sequences
from collections import Counter
word_counts = Counter(words)
top_three = word_count.most_common(3)
print(top_three)
```

* Some sorting techniques:

```python
#dict
sorted(rows,key=itemgetter('fname')
#obj
sorted(users, key=attrgetter('user_id')
```

* extract subset of a Dictionary `p1 = dict((key,value) for key,value in prices.items() if value > 200)`

### SA tips

* Terminating a program with an error message `raise SystemExit('It failed')` 

* Putting limits on Memory and CPU Usage

```python
import signal
import resource
import os

def time_exceed(signo, frame):
    priint('Time is up!')
    raise SystemExit(1)

def set_max_runtime(seconds):
    soft, hard = resource.getrlimit(resource.RLIMIT_CPU)
    resource.setrlimit(resource.RLIMIT_CPU, (seconds, hard));
    signal.signal(signal.SIGXCPU, time_exceed)

if __name__ == '__main__':
    set_max_runtime(15)
        while True:
            pass


def limit_memory(maxsize):
    soft, hard = resource.getrlimit(resource.RLIMIT_AS)
    resource.setrlimit(resource.RLIMIT_AS, (maxsize,hard))
```