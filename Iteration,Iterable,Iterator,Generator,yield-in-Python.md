---
title: 理解Python中的迭代、可迭代对象、迭代器、生成器、yiled专题
date: 2017-04-24 19:11:25
categories:
- Python
tags:
- iteration
- iterable
- iterator
- generator
- yield
---
# Iteration, Iterable, Iterator, Generator, yield in Python

## 迭代(Iteration)
**迭代**，指的是按照某种顺序逐个访问列表中的每一项。

在 Python 中，用` for` 来实现迭代，它的结构是 `for ... in ...`，实际是利用可迭代对象`Iterable`得到迭代器`Iterator`，再重复调用 `__next__ `方法实现的。

![](http://nvie.com/img/iterable-vs-iterator.png)



## 可迭代对象(Iterable)
可迭代对象`Iterable`就是实现了`__iter__`方法，可以返回一个迭代器`Iterator`，或者定义了`__getitem__`方法，可以按` index` 索引（且能够在没有值时抛出一个 `IndexError` 异常）的任意对象。

> [**Stackoverflow:**](http://stackoverflow.com/questions/9884132/what-exactly-are-pythons-iterator-iterable-and-iteration-protocols)
>
> An **iterable** is an object that has an `__iter__` method which returns an **iterator**, or which defines a `__getitem__` method that can take sequential indexes starting from zero (and raises an `IndexError` when the indexes are no longer valid). So an **iterable** is an object that you can get an **iterator** from.

可迭代对象具有如下的特性：

- 可以`for`循环： `for i in iterable`
- 可以按`index`索引：定义了`__getitem__`方法，比如`list,str`
- 可以通过调用`__iter__`方法或者被`iter(obj)`调用，返回一个迭代器`Iterator`

可以通过`isinstance(obj, collections.Iterable)` 来判断对象是否为可迭代对象。

>
>**NOTE:**
>Often, for pragmatic reasons, iterable classes will implement both `__iter__()` and `__next__()` in the same class, and have `__iter__()` return `self`, which makes the class both an iterable and its own iterator. It is perfectly fine to return a different object as the iterator, though.


## 迭代器(Iterator)
迭代器`Iterator`就是实现了`next (Python 2)` 或者 `__next__ (Python 3)` 方法（并且能够在没有值时抛出一个 `StopIteration`异常）的任意对象。

如果需要自定义迭代器，则需要满足如下迭代器协议：

- 定义了`__iter__`方法，但是必须返回自身
- 定义了`__next__`方法。用来返回下一个值，并且当没有数据了，抛出 `StopIteration`
- 可以保持当前的状态

可以通过 `isinstance(obj, collections.Iterator)` 来判断对象是否为迭代器。

## 生成器(Generator)
生成器`Generator`是创建一个迭代器最简单的方式。在普通的函数中使用`yield`代替`return`，这个函数将会返回一个生成器。

生成器是可以迭代的，但是你 **只可以读取它一次** ，因为它并不把所有的值放在内存中，而是实时地生成数据。

![](http://ww4.sinaimg.cn/mw690/c3c88275gw1f3zbjmfejfj20zc0fytay.jpg)

### 两种创建方式

#### 包含`yield`的函数(generator function)

yield 是一个类似 return 的关键字，只是这个函数返回的是个生成器。当你调用这个函数的时候，函数内部的代码并不立马执行 ，这个函数只是返回一个生成器对象。

生成器函数跟普通函数只有一点不一样，就是把 `return` 换成`yield`,其中`yield`是一个语法糖，内部实现了迭代器协议，同时保持状态可以挂起。`yield`是数据的生产者，而诸如`for`等是数据的消费者。

```python
def generator_func():
    yield 'step 1'
    yield 'step 2'
    yield 'step 3'
    
gen = generator_func()
gen.__next__()
# step 1
gen.__next__()
# step 2
gen.__next__()
# step 3
gen.__next__()
# StopIteration
```

#### 生成器表达式(generator expression)

```python
gen = (i*i for i in range(2, 4))
gen
# <generator object <genexpr> at 0x10f6e2af0>
gen.__next__()
# 4
gen.__next__()
# 9
gen.__next__()
# StopIteration
```
### 三个内置方法(3 build-in methods)

#### close()

`close()`方法可以关闭生成器。生成器被关闭后，再次调用`__next__()`方法，不管能否遇到yield关键字，都会立即抛出StopIteration异常。

```python
gen = (i*i for i in range(2, 4))
gen.close()
gen.__next__()
# StopIteration
```

#### send()

可以通过`send()`方法，向生成器内部传递参数。

```python
def consumer():
    print('ready to receive msg')
    while True:
        msg = yield
        print('got msg: %s' % msg)

con = consumer()
con.__next__() # con.send(None)
# ready to receive msg
con.send('msg 1')
# got msg: msg 1
con.send('msg 2')
# got msg: msg 2
con.close()
con.send('msg 3')
# StopIteration
```

需要注意的是，调用`send()`方法之前，必须先调用`__next__()`方法或者`send(None)`，让生成器执行到`yield`处以接受参数。否则将抛出异常`can't send non-None value to a just-started generator`。其实`__next()__`的实现，就是`send(None)`。

#### throw()

除了向生成器函数内部传递参数，我们还可以传递异常。

```python
def throw_gen():
    try:
        yield 'Normal'
    except ValueError:
        yield 'Error'
    finally:
        print('Finally')


gen = throw_gen()
print(gen.__next__())  
# Normal
print(gen.__next__())  
# Finally 
# StopIteration
```

```python
gen = throw_gen()
print gen.next()  
# Normal
print gen.throw(ValueError)    
# Error
print gen.next()  
# Finally
# StopIteration
```

# todo 

- yield from
- Coroutine
- Asynchronous generator functions
- Awaitable, AsyncIterator, AsyncIterable

## 参考资料

- http://kuanghy.github.io/2016/05/18/python-iteration
- https://eastlakeside.gitbooks.io/interpy-zh/content/Generators/
- https://docs.python.org/3/tutorial/classes.html#iterators
- http://www.cnblogs.com/huxi/archive/2011/07/01/2095931.html
- https://foofish.net/iterators-vs-generators.html
- http://nvie.com/posts/iterators-vs-generators/
- http://pyzh.readthedocs.io/en/latest/the-python-yield-keyword-explained.html#id3
- http://www.bjhee.com/python-yield.html

