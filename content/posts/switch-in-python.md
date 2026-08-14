---
categories:
    - python
date: 2020-06-23T10:43:33Z
description: Let's use switch in Python.
keywords: switch, switch case in python, match case
lastmod: 2026-08-14T00:00:00Z
tags:
    - python
    - OOP
title: How to use switch in Python?
---



# To implement a switch structure in Python

```python
#!/usr/bin/env python
# -*- coding: utf-8 -*-

switch = {
    'add': lambda x, y: x + y,
    'sub': lambda x, y: x - y,
    'mul': lambda x, y: x * y,
    'div': lambda x, y: x / y,
}

if __name__ == '__main__':
    print(switch['add'](1, 8))
    print(switch['sub'](1, 8))
    print(switch['mul'](1, 8))
    print(switch['div'](1, 8))

```

```text
9
-7
8
0.125
```

# Modern alternative: Python 3.10+ `match/case`

> This article was written before Python 3.10 (released Oct 2021). If you are on
> Python 3.10 or newer, use the native `match` statement instead of a dict of lambdas.

```python
#!/usr/bin/env python

def calculate(operator: str, x: float, y: float) -> float:
    match operator:
        case 'add':
            return x + y
        case 'sub':
            return x - y
        case 'mul':
            return x * y
        case 'div':
            return x / y
        case _:  # default
            raise ValueError(f"unknown operator: {operator}")

if __name__ == '__main__':
    print(calculate('add', 1, 8))
    print(calculate('sub', 1, 8))
    print(calculate('mul', 1, 8))
    print(calculate('div', 1, 8))
```

Same output as above. The dict-of-lambdas trick still works, but `match` is the idiomatic
Python 3.10+ way, and it can also match on data structures and guards, not just strings.
