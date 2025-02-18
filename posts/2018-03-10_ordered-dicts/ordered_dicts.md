---
title: "Python Dictionaries Are Now Ordered. Keep Using OrderedDict."
date: "2018-03-10"
categories: [python, pandas]
image: "dict_ordering.png"
description: "Python dictionaries are now ordered, but you should still use OrderedDict for better readability and interoperability."
---

Until recently, Python dictionaries did not preserve the order in which items were added to them. For instance, you might type `{'fruits': ['apples', 'oranges'], 'vegetables': ['carrots', 'peas']}` and get back `{'vegetables': ['carrots', 'peas'], 'fruits': ['apples', 'oranges']}`. If you wanted a dictionary that preserved order, you could use the `OrderedDict` class in the standard library module `collections`.

However, this situation is changing. **Standard `dict` objects preserve order in the reference (CPython) implementations of Python 3.5 and 3.6, and this order-preserving property is becoming a language feature in Python 3.7.**

You might think that this change makes the `OrderedDict` class obsolete. However, there are at least two good reasons to continue using `OrderedDict`. First, relying on standard `dict` objects to preserve order will cause your code to break on versions of CPython earlier than 3.5 and on some alternative implementations of Python 3.5 and 3.6. Second, **using an `OrderedDict` communicates your intention** to rely on the order of items in your dictionary being preserved, both to human readers of your code and to the third-party libraries you call within it.

In fact, **some tools that are widely used in data science assume that the order of items in a dict is not significant.**

## IPython Assumes That `dict` Order Doesn't Matter

Consider this example `IPython` session:

```python
In [1]: from string import ascii_lowercase

In [2]: my_dict = {letter: [1] for letter in ascii_lowercase[::-1]}

In [3]: print(my_dict)
{'z': [1], 'y': [1], 'x': [1], 'w': [1], 'v': [1], 'u': [1], 't': [1], 's': [1], 'r': [1], 'q': [1], 'p': [1], 'o': [1], 'n': [1], 'm': [1], 'l': [1], 'k': [1], 'j': [1], 'i': [1], 'h': [1], 'g': [1], 'f': [1], 'e': [1], 'd': [1], 'c': [1], 'b': [1], 'a': [1]}

In [4]: my_dict
Out[4]:
{'a': [1],
'b': [1],
'c': [1],
'd': [1],
'e': [1],
'f': [1],
'g': [1],
'h': [1],
'i': [1],
'j': [1],
'k': [1],
'l': [1],
'm': [1],
'n': [1],
'o': [1],
'p': [1],
'q': [1],
'r': [1],
's': [1],
't': [1],
'u': [1],
'v': [1],
'w': [1],
'x': [1],
'y': [1],
'z': [1]}
```

Here it appears that `my_dict` does not in fact preserve order consistently. What is actually happening, however, is that the IPython kernel sorts the dictionary keys for display when the dictionary is evaluated. **IPython assumes that the ordering of items in a dict object is not significant.** By contrast, it does not sort the keys in an `OrderedDict`:

```python
In [5]: from collections import OrderedDict

In [6]: my_ordered_dict = OrderedDict([(letter, [1]) for letter in ascii_lowercase[::-1]])

In [7]: print(my_ordered_dict)
OrderedDict([('z', [1]), ('y', [1]), ('x', [1]), ('w', [1]), ('v', [1]), ('u', [1]), ('t', [1]), ('s', [1]), ('r', [1]), ('q', [1]), ('p', [1]), ('o', [1]), ('n', [1]), ('m', [1]), ('l', [1]), ('k', [1]), ('j', [1]), ('i', [1]), ('h', [1]), ('g', [1]), ('f', [1]), ('e', [1]), ('d', [1]), ('c', [1]), ('b', [1]), ('a', [1])])

In [8]: my_ordered_dict
Out[8]:
OrderedDict([('z', [1]),
('y', [1]),
('x', [1]),
('w', [1]),
('v', [1]),
('u', [1]),
('t', [1]),
('s', [1]),
('r', [1]),
('q', [1]),
('p', [1]),
('o', [1]),
('n', [1]),
('m', [1]),
('l', [1]),
('k', [1]),
('j', [1]),
('i', [1]),
('h', [1]),
('g', [1]),
('f', [1]),
('e', [1]),
('d', [1]),
('c', [1]),
('b', [1]),
('a', [1])])
```

(The relevant bit of `IPython` code is in its [_dict_pprinter_factory function](https://github.com/ipython/ipython/blob/main/IPython/lib/pretty.py#L676).)

## Pandas Assumes That `dict` Order Doesn't Matter

**Update:** [`pandas 0.23.0`](https://pandas.pydata.org/pandas-docs/stable/whatsnew.html#whatsnew-0230-api-breaking-dict-insertion-order) (released May 15, 2018) does respect ordering when initializing a `DataFrame` or `Series` with a `dict` in Python 3.6+.

**`pandas` also assumes that the ordering of items in a `dict` object is not significant.** If you initialize a `DataFrame` with a `dict`, it sorts the items by key:

```python
In [9]: import pandas as pd

In [10]: pd.DataFrame(my_dict)
Out[10]:
a b c d e f g h i j ... q r s t u v w x y z
0 1 1 1 1 1 1 1 1 1 1 ... 1 1 1 1 1 1 1 1 1 1

[1 rows x 26 columns]
```

It does not sort the columns if you use an `OrderedDict`:

```python
In [11]: pd.DataFrame(my_ordered_dict)
Out[11]:
z y x w v u t s r q ... j i h g f e d c b a
0 1 1 1 1 1 1 1 1 1 1 ... 1 1 1 1 1 1 1 1 1 1

[1 rows x 26 columns]
```

(The relevant bit of `pandas` code is in the `DataFrame` class’s [`_init_dict` method](https://github.com/pandas-dev/pandas/blob/master/pandas/core/frame.py).)

## How Adding Guarantees Can Be Dangerous

These examples illustrate a surprising phenomenon: **adding a guarantee can be dangerous. Users cannot rely on that guarantee until its existence becomes common knowledge and the implications of that common knowledge make their way through the community and into the relevant infrastructure**.

This process will take a particularly long time in the case of the Python `dict` ordering guarantee because legacy Python 2.7 continues to be widely used and because many open-source packages are not well maintained. As a result, **I expect that using `OrderedDict` to communicate intent to other developers and third-party libraries will remain a best practice for several years.**
