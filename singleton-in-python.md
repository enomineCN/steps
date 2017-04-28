---
title: 单利模式Python下的几种实现方式
date: 2017-04-17 09:27:22
categories: 
- Python
tags: 
- singleton
---
# Singleton in Python
>本文所有代码未考虑线程安全
>
>大部分代码来源于[stackoverflow-Creating a singleton in Python](http://stackoverflow.com/questions/6760685/creating-a-singleton-in-python)
## 1.class decorator

```python
class Singleton:
    def __init__(self, cls):
        self.cls = cls
        self.instance = None
        self.cls()

    def __call__(self, *args, **kwds):
        if self.instance is None:
            self.instance = self.cls(*args, **kwds)
        return self.instance


@Singleton
class MyClass:
    pass
```
## 2.function decorator
```python
def singleton(cls):
    instances = {}

    def get_instance(*args, **kwargs):
        if cls not in instances:
            instances[cls] = cls(*args, **kwargs)
        return instances[cls]

    return get_instance


@singleton
class MyClass:
    pass
```

## 3.metaclass
```python
class Singleton(type):
    _instances = {}

    def __call__(cls, *args, **kwargs):
        if cls not in cls._instances:
            cls._instances[cls] = super(Singleton, cls).__call__(*args, **kwargs)
        return cls._instances[cls]
    
    
class MyClass(metaclass=Singleton):
    pass
```
## 5.base class
```python
class Singleton:
    _instance = None

    def __new__(cls, *args, **kwargs):
        if not isinstance(cls._instance, cls):
            cls._instance = object.__new__(cls)
        return cls._instance
    
    
class MyClass(Singleton):
    pass
```
## 6.class attribute
```python
class WebSoupClient:
    def __init__(self, api_url):
        self.api_url = api_url


class API:
    _clients = {}

    def __init__(self, api_url, user_id, password):
        super().__init__()
        self.api_url = api_url

    @property
    def client(self):
        if self.api_url not in self._clients:
            self._clients[self.api_url] = WebSoupClient(self.api_url)
        return self._clients[self.api_url]
```

