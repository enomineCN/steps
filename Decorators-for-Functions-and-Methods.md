---
title: Python装饰器之函数的装饰器
date: 2017-04-17 09:27:22
categories: 
- Python
tags: 
- decorator
---
# Decorators for Functions and Methods

## 1.函数实现装饰函数的装饰器（Function Decorators） 
### 1.1函数不带参数
```python
def decorator(fun):
    def wrapper():
        print('into fun decorator')
        fun()
    return wrapper

@decorator
def echo():
    print('into echo')

echo()
# echo() => decorator(echo)() => wrapper()
```
### 1.2函数带参数

```python
def decorator(fun):
    def wrapper(*args, **kwargs):
        print('into fun decorator')
        print(*args, **kwargs)
        fun(*args, **kwargs)
    return wrapper


@decorator
def echo(name, age):
    print('into echo ' + name + ' ' + age)

echo('nike', '20')
# echo('nike', '20') => decorator(echo)('nike', '20') => wrapper('nike', '20')
```
### 1.3装饰器带参数

```python
def decorator(*o_args, **o_kwargs):
    print('into outer fun decorator')
    print(*o_args, **o_kwargs)

    def outer_wrapper(fun):
        def wrapper(*args, **kwargs):
            print('into fun decorator')
            print(*o_args, **o_kwargs)
            print(*args, **kwargs)
            fun(*args, **kwargs)
        return wrapper
    return outer_wrapper


@decorator('hi', 'decorator')
def echo(name, age):
    print('into echo ' + name + ' ' + age)

echo('nike', '20')
# echo('nike', '20') => decorator('hi', 'decorator')(echo)('nike', '20')
# => outer_wrapper(echo)('nike', '20') => wrapper('nike', '20')
```

### 1.4@functools.wraps
```python
import functools


def decorator(fun):
    @functools.wraps(fun)
    def wrapper():
        print('into fun decorator')
        fun()
    return wrapper


@decorator
def echo():
    print('into echo')

print(echo.__name__)
```
## 2.类实现装饰函数的装饰器（Class Decorators）
###2.1函数不带参数
```python
class Decorator:
    def __init__(self, fun):
        print('__init__')
        self.fun = fun

    def __call__(self):
        print('__call__')
        self.fun()


@Decorator
def echo():
    print('into echo')

echo()
echo()
# def echo() => Decorator.__init__(echo)
# echo() => Decorator.__call__()
```

### 2.2函数带参数

```python
class Decorator:
    def __init__(self, fun):
        print('__init__')
        print(fun)
        self.fun = fun

    def __call__(self, *args, **kwargs):
        print('__call__')
        print(args, kwargs)
        self.fun(*args, **kwargs)


@Decorator
def echo(name):
    print('into echo' + name)

echo('2')
echo('3')
# echo(2) = Decorator(echo)(2) = Decorator.__init__(echo).__call__(2)
```

### 2.3装饰器带参数 

```python
class Decorator:
    def __init__(self, *args, **kwargs):
        print('__init__')
        print(args, kwargs)

    def __call__(self, fun):
        print('__call__')

        def wrapper(*args, **kwargs):
            print('into wrapper')
            fun(*args, **kwargs)
        return wrapper


@Decorator('1', '2')
def echo(name):
    print('into echo' + name)

echo('3')
echo('4')
# echo('2') = wrapper('2')
# echo('2') = Decorator('1')(echo)('2') = Decorator.__init__('1').__call__(echo).__call__('2')
```

