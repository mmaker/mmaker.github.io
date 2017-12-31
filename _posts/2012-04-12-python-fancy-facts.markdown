---
layout: post
title:  "Python Fancy Facts"
date:   2012-04-12 12:00:00 +0100
categories: python
---

Back in 2012 I did a presentation of bizarre, counterintuitive things you can do
with python.
It turned out to be unintelligible for most of the audience there. I hope however
somebody on the internet will be able to use it for fun and profit™.

Oh, the initial subtitle for this was:

<p style="text-align: center"><i>
An asyncronous spacetime travel through metaclasses, iterators and antigravity.
</i></p>

{% highlight pycon %}
tumbolandia:cpython maker$ python
Python 2.7.1 (r271:86832, Jun 25 2011, 05:09:01)
>>> True, False = False, True
>>> (True == True) == False  # ex falso quodlibet
True
>>> ^D
tumbolandia:cpython maker$ ./python.exe
Python 3.3.0a2+ (default, Apr 12 2012, 09:33:37)
>>> True <> False
  File "", line 1
    True <> False
          ^
SyntaxError: invalid syntax
>>> from __future__ import barry_as_FLUFL # (time-travel) PEP 401
>>> True <> False
True
>>>
>>> from __future__ import braces
  File "", line 1
SyntaxError: not a chance
>>> import antigravity  # if you read the source it's just 1 line of code!
>>>
>>>
>>> 'this is a string'
'this is a string'
>>> [1, '2', 3.]
[1, '2', 3.0]
>>> {1, 2, 2, 3}
{1, 2, 3}
>>> (1, 2, 3)
(1, 2, 3)
>>> {'one':1}
{'one': 1}
>>> ...
Ellipsis
>>>
>>> def f(a, b, c, d):   # Arithmetic progression
...   return list(range(a, d, b-a)) if c is Ellipsis else [a, b, c, d]
...
>>> f(1, 2, ..., 10)
[1, 2, 3, 4, 5, 6, 7, 8, 9]
>>> f(1, 3, ..., 10)
[1, 3, 5, 7, 9]
>>>
>>> def f(l=[]):
...   l.append(1)
...   return l
...
>>> f()
[1]
>>> f()
[1, 1]
>>> f()
[1, 1, 1]
>>> f() # wtf? Mutable and immutable objects.
[1, 1, 1, 1]
>>> t = (0, 1, [])  # mutable inside immutable
>>> t[2].append(2)  # this used to raise an error but add the element anyways
>>> t
(0, 1, [2])
>>>
>>> def mysum(x):  # closures!
...   def inner(y):
...     return x+y
...   return inner
...
>>> sum1 = mysum(1)
>>> sum1(2)
3
>>> def fib(n):  # decorators!
...   return 1 if n in (1, 2) else fib(n-1) + fib(n-2)
...
>>> fib(100) # *slow*
Traceback (most recent call last):
  File "", line 1, in
  File "", line 2, in fib
  File "", line 2, in fib
  File "", line 2, in fib
  File "", line 2, in fib
  File "", line 2, in fib
  File "", line 2, in fib
  [. . . . . ]
  File "", line 2, in fib
KeyboardInterrupt
>>> from functools import lru_cache
>>> @lru_cache(maxsize=100)
... def fib(n):  # just as before
...   return 1 if n in (1, 2) else fib(n-1) + fib(n-2)
...
>>> fib(100) # *fast*
354224848179261915075
>>>
>>>
>>> [x for x in range(10)] # listcomp
[0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
>>> [(lambda x: x+i) for i in range(10)]
[<function .<lambda> at 0x10067aa70>, <function .<lambda> at 0x10067ab00>, <function .<lambda> at 0x10067ab90>, <function .<lambda> at 0x10067ac20>, <function .<lambda> at 0x10067acb0>, <function .<lambda> at 0x10067ad40>, <function .<lambda> at 0x10067add0>, <function .<lambda> at 0x10067ae60>, <function .<lambda> at 0x10067aef0>, <function .<lambda> at 0x10067af80>]
>>> [(lambda x: x+i)(10) for i in range(10)]
[10, 11, 12, 13, 14, 15, 16, 17, 18, 19]
>>> [(lambda x: x+i) for i in range(10)][0](10) # I would expect 10.
19
>>> [mysum(i) for i in range(10)][0](10) # I would expect 10.
10
>>>
>>>
>>>
>>> help(object) # "the most base type"

>>> class A: pass
...
>>> a = A()
>>> a.x = 1
>>> a.x
1
>>> a.__dict__
{'x': 1}
>>> a.__dict__['x'] = 3
>>> a.x
3
>>> class A:
...   x = 1
...
>>> a = A()
>>> a.__dict__ # where is a.x ?
{}
>>> a.x
1
>>> A.__dict__ # it's here! (classes are objects)
dict_proxy({'__doc__': None, '__qualname__': 'A', '__module__': '__main__', '__weakref__': <attribute '__weakref__' of 'A' objects>, 'x': 1, '__dict__': <attribute '__dict__' of 'A' objects>})
>>>
>>>
>>> class Point:
...   __slots__ = ('x', 'y')   # optimization removing __dict__ and __weakref__
...
>>>
>>> p = Point()
>>> p.x = 1
>>> p.a = 1
Traceback (most recent call last):
  File "", line 1, in
AttributeError: 'Point' object has no attribute 'a'
>>>
>>>
>>> type
<class 'type'>
>>> type(p)
<class '__main__.Point'>
>>> type(type(p)) # reaching meta
<class 'type'>
>>> type('Foo', (object, ), {}) # a new, synthetic class
<class '__main__.Foo'>
>>>
>>>
>>>
>>> def meta(name, bases, attrs): # a metaclass is a callable
...   return type(name, bases, {key.title():val for key, val in attrs.items()}) # dict comp
...
>>> class Java(metaclass=meta):
...   oh_my_god = 'whoa'
...
>>> j = Java()
>>> j.Oh_My_God
'whoa'
>>> j.oh_my_god
Traceback (most recent call last):
  File "", line 1, in
AttributeError: 'Java' object has no attribute 'oh_my_god'
>>>
>>>
>>> def gen():
...   yield 1 # generators !
...   yield 2
...
>>> it = gen()
>>> next(it)
1
>>> next(it)
2
>>> next(it)
Traceback (most recent call last):
  File "", line 1, in
StopIteration
>>> def gen():
...   yield from [1, 2, 3]
...
>>> it = gen()
>>> next(it)
1
>>> next(it)
2
>>> next(it)
3
>>> next(it)
Traceback (most recent call last):
  File "", line 1, in
StopIteration
>>>
>>> def gen(n): # async functions
...   while True:
...     if n == 0: return # return inside a generator
...     n = (yield n-1) or n-1
...
>>> gen(10)
<generator object gen at 0x101010f50>
>>> it = gen(10)
>>> next(it)
9
>>> next(it)
8
>>> next(it)
7
>>> it.send(10)
9
>>> next(it)
8
>>> next(it)
7
[ . . . ]
>>> next(it)
2
>>> next(it)
1
>>> next(it)
0
>>> next(it)
Traceback (most recent call last):
  File "", line 1, in
StopIteration
>>>
{% endhighlight %}
