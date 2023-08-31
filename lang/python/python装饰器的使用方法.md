[toc]

## 前言
装饰器在 python 中使用的频率非常高，它可以在不改动原有函数的基础上对其进行增强功能。

下面主要是介绍装饰器的各种用法，并理解其运行过程。

## 1. 函数装饰器

### 1.1 在函数上添加装饰器

decro 是一个装饰器函数，其实现是将内部的函数 wrapper 作为返回值返回出去。

在函数 test 上添加 @decro 进行使用，可以将本函数作为一个参数传入到 decro 函数中，然后，然后得到的是装饰器函数内部返回的函数 wrapper, 我们在调用 test 方法时，其实调用的是装饰器返回的 wrapper 函数，该函数中会调用被装饰的函数 test

```py
def decro(func):  
    def wrapper(*args, **kwargs):  
        print("wrapper")  
        return func()  
    return wrapper

@decro  
def test():  
    print("test")  

test()
```

### 1.2 装饰器的运行过程

装饰器时在被装饰的函数定义之后立即运行的，当执行到@decro 装饰 test 函数时，会马上执行函数 decro，然后将 wrapper 给返回出去。

```py
def decro(func):  
    print("decro")  
  
    def wrapper(*args, **kwargs):  
        print("wrapper")  
        return func()  
  
    return wrapper  
  
print("start")  
  
@decro  
def test():  
    print("test")  
  
print("end")

test()
```

输出如下，可以看到先执行了 start，然后马上执行了装饰器 decro，然后再执行 end，当我们调用 test 函数时，执行了装饰器内部函数 wrapper，然后再调用被装饰的函数 test

 ```
 start
 decro
 end
 wrapper
 test
 ```

### 1.3 保存原函数信息

在使用装饰器时，调用的原方法已经被替换为装饰器返回的新方法了，所以方法的元信息已经被替换了, 通过 name、doc 得到的元数据已经被替换成了新方法的。

```python
def decro(func):  
    def wrapper(*args, **kwargs):  
        """ wrapper doc """  
        print("wrapper")  
        return func(*args, **kwargs)  
  
    return wrapper  
  
  
@decro  
def test():  
    """ test doc """  
    print("test")  
  
  
print("test's __name__ = {}".format(test.__name__))  
print("test's __doc__ = {}".format(test.__doc__))
```

输出如下，会发现函数 test 的函数信息 `__name__` 和 `__doc__` 变成 wrapper 的信息。

```
test's __name__ = wrapper
test's __doc__ =  wrapper doc
```

但是我们不想要改变原方法的元信息，这个时候需要使用 `functools.wraps` 解决。

```python
from functools import wraps  
  
def decro(func):  
  
    @wraps(func)  
    def wrapper(*args, **kwargs):  
        """ wrapper doc """  
        print("wrapper")  
        return func(*args, **kwargs)  
  
    return wrapper  
  
  
@decro  
def test():  
    """ test doc """  
    print("test")  
  
  
print("test's __name__ = {}".format(test.__name__))  
print("test's __doc__ = {}".format(test.__doc__))

```

输出如下，会发现，test 函数信息没有被替换掉，保证了函数的原汁原味。

```
test's __name__ = test
test's __doc__ =  test doc 
```

### 1.4 调用原函数

装饰器可以增强函数的功能，但是在某些场景我就想要使用原函数，而不想使用装饰之后的函数，可以通过调用__wrapped__来调用原函数。

```python
from functools import wraps  
  

def decro(func):  
    @wraps(func)  
    def wrapper(*args, **kwargs):  
        """ wrapper doc """  
        print("wrapper")  
        return func(*args, **kwargs)  
  
    return wrapper  
  

@decro  
def test():  
    """ test doc """  
    print("test")  
  
  
test.__wrapped__()
```

### 1.5 带参数的装饰器

还有这么一种场景，我们想要在装饰器中添加参数。

想要通过参数决定日志级别，这里的 logged 接收 level 参数并将它作用在内部函数中，返回值是将 decro 函数返回，然后再将函数 test 作为参数从传入到 decro 函数中，再将 wrapper 返回，最终 test 还是替换成了 wrapper，在该方法中使用了传入的 ERROR 的日志级别。

```python
from functools import wraps  
import logging  
  
  
def logged(level):  
    def decro(func):  
        @wraps(func)  
        def wrapper(*args, **kwargs):  
            """ wrapper doc """  
            logging.log(level, func.__name__)  
            return func(*args, **kwargs)  
  
        return wrapper  
  
    return decro  
  
  
@logged(level=logging.ERROR)  
def test():  
    """ test doc """  
    print("test")  
  
  
test()
```

输出了 ERROR 日志级别的日志：

```plaintext
ERROR:root:test
test
```

### 1.6 带可选参数的装饰器
上面实现的装饰器是必须要带上参数的，但是有的时候，我们不需要带参数，那么该如何实现？

装饰器的 func 默认值为 None，当传入 level 参数时，则返回偏函数 partial ，该函数会基于 logged 创建一个仅包含 level 的新的函数，这个新的函数作为新的装饰器来装饰 add 函数。

当没有传入 level 参数时，就和普通的装饰器一样使用。

```python
from functools import wraps, partial  
import logging  
  
def logged(func=None, level=logging.INFO):  
    if func is None:  
        return partial(logged, level=level)  
  
    @wraps(func)  
    def wrapper(*args, **kwargs):  
        logging.log(level, func.__name__)  
        return func(*args, **kwargs)  
  
    return wrapper  
  
@logged(level=logging.ERROR)  
def add(a, b):  
    print("add")  
    return a + b  
  
  
@logged  
def add2(a, b):  
    print("add2")  
    return a + b  
  
  
print(add(1, 2))  
print("-" * 10)  
print(add2(1, 2))
```

输出如下，add 函数的装饰器传入了日志级别为 ERROR 的参数，输出了 ERROR 的日志，而add2 没有。

```
ERROR:root:add
add
3
----------
add2
3
```

### 1.7 为类添加装饰器

上面都是使用装饰器来增强函数的功能，但它还可以增强类的功能，对类进行装饰。

下面的例子中，decro 将被装饰的类 Demo 传入进来，主要是将其类中 `__getattribute__` 方法替换成了 `new_getattribute` 方法。

```python
def decro(cls):  
    orig_getattribute = cls.__getattribute__  
  
    def new_getattribute(self, name):  
        print("get name = {}".format(name))  
        return orig_getattribute(self, name)  
  
    cls.__getattribute__ = new_getattribute  
    return cls  
  
  
@decro  
class Demo:  
  
    def __init__(self, num):  
        self.num = num  
  
  
d = Demo(1)  
print(d.num)
```

输出如下，获取 num 的值时，调用了装饰器替换的 new_getattribute 方法。

```
get name = num
1
```

## 2. 类装饰器

### 2.1 基础写法

之前都是使用函数方法来定义装饰器，但其实也可以通过类来定义装饰器。

在类装饰器中定义__init__方法，被它装饰的函数会被传入到 func 参数中，这个时候该类装饰器已经被实例化了，也就是将该实例对象替换了被装饰的函数 say。

当我们调用 say 函数时，其实调用的是类装饰器的对象，这个时候会调用__call__方法，该方法中可以对原函数进行增强，并进行调用原方法。

```python
class logger(object):  
    def __init__(self, func):  
        self.func = func  
  
    def __call__(self, *args, **kwargs):  
        print("the function {func}() is running...".format(func=self.func.__name__))  
        return self.func(*args, **kwargs)  
  
  
@logger  
def say(something):  
    print("say {}!".format(something))  
  
  
say("hello")
```

输出如下：

```
the function say() is running...
say hello!
```

### 2.2 暴露被装饰的元信息

这个时候会出现和函数装饰器一样的问题，那就是被装饰的函数的元信息已经被替换掉了，这个时候我们还是想保留原有的原信息。

还是使用 wraps 函数来解决该问题。

```python
from functools import wraps
class logger(object):  
    """ logger doc """  
  
    def __init__(self, func):  
        wraps(func)(self)  
  
    def __call__(self, *args, **kwargs):  
        print("the function {func}() is running...".format(func=self.__wrapped__.__name__))  
        return self.__wrapped__(*args, **kwargs)  
  
  
@logger  
def say(something):  
    """ say doc """  
    print("say {}!".format(something))  
  
  
say("hello2")  
print(say.__doc__)
```

输出的是 say 方法的 doc：

```
the function say() is running...
say hello2!
 say doc 
```

### 2.2 带参数的类装饰器

那么带参数的类装饰器该如何实现呢？

在 `__init__` 方法中接收装饰器传入的参数，保存起来，然后再通过 `__call__` 函数将内部函数 wrapper 给返回出去，这个时候被装饰的函数已经被 wrapper 给替换了，所以通过@wraps 还原函数的`__name__`。

```python
from functools import wraps

class logger(object):  
    def __init__(self, level="INFO"):  
        self.level = level  
  
    def __call__(self, func):  
        @wraps(func)
        def wrapper(*args, **kwargs): 
            print("[{level}]: the function {func}() is running...".format(level=self.level, func=func.__name__)) 
            func(*args, **kwargs)  
  
        return wrapper  
  
  
@logger(level="ERROR")  
def say(something):  
    print("say {}!".format(something))  
  
  
say("hello2")
print(say.__name__)
```

输出如下，调用 say 函数也就是调用 wrapper 函数：

```
[ERROR]: the function say() is running...
say hello2!
```

## 3. 总结
装饰器的用法很多，封装成库，给其他人使用也非常的方便，我们需要理解它的运行过程，才能更好的使用它。

## 4.  参考资料
https://python3-cookbook.readthedocs.io/zh_CN/latest/c09/p01_put_wrapper_around_function.html
