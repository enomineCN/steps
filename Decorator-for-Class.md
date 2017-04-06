#  Decorator for Class

## 1.Class Decorators

### 1.1装饰器不带参数
```python
class Decorator:
    def __init__(self, cls):
        print('D.__init__')
        self.cls = cls

    def __call__(self, *args, **kwargs):
        print('D.__call__')
        return self.cls(*args, **kwargs)


@Decorator
class A:
    def __new__(cls, *args, **kwargs):
        print('A.__new__')
        return object.__new__(cls)

    def __init__(self, *args, **kwargs):
        print('A.__init__')
        self.args = args
        self.kwargs = kwargs
A()
A('1')
```

### 1.2装饰器带参数1

```python
class Decorator:
    def __init__(self, *args, **kwargs):
        print('D.__init__')
        self.args = args
        self.kwargs = kwargs

    def __call__(self, cls):
        print('D.__call__')
        return cls


@Decorator(1, 2)
class A:
    def __new__(cls, *args, **kwargs):
        print('A.__new__')
        return object.__new__(cls)

    def __init__(self, *args, **kwargs):
        print('A.__init__')
        self.args = args
        self.kwargs = kwargs

A()
A('1')
```

### 1.3装饰器带参数2

```python
class Decorator:
    def __init__(self, *args, **kwargs):
        print('D.__init__')
        self.args = args
        self.kwargs = kwargs

    def __call__(self, cls):
        print('D.__call__')

        def wrapper(*args, **kwargs):
            print('wrapper')
            cls(*args, **kwargs)
        return wrapper


@Decorator(1, 2)
class A:
    def __new__(cls, *args, **kwargs):
        print('A.__new__')
        return object.__new__(cls)

    def __init__(self, *args, **kwargs):
        print('A.__init__')
        self.args = args
        self.kwargs = kwargs

A()
A('1')
A('2')
```

## 2.Function Decorators

```python
def Decorator(*args, **kwargs):
    print(args, kwargs)

    def wrapper(cls):
        print('wrapper')
        return cls
    return wrapper


@Decorator()
class A:
    def __new__(cls, *args, **kwargs):
        print('A.__new__')
        return object.__new__(cls)

    def __init__(self, *args, **kwargs):
        print('A.__init__')
        self.args = args
        self.kwargs = kwargs

A()
A('2')
```

