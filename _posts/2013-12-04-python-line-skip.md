---
layout: post
title: Python extract n line from the input file
---

Using a generator, we can process the file reading job more elegantly.

Well, Python views the open file as a generator obj. So we can warp it into another function.

For example:

```python
#only proceed the first n line in python
def skip_line(input_file,n):
    for i in range(n):
        next(input_file)
```

Just as simple as this!