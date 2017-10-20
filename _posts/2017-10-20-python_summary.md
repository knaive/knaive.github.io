---
layout: post
title: Python summary
---

# slots
## 类变量__slots__被定义为一个可遍历类型(tuple, list..), 只有名字在其中的instance变量才能被定义
```
class Foo(object):
    __slots__ = ('name')

foo = Foo()
foo.name = 'foo'    # Right
foo.value = 'value' # AttributeError
```
## 只对继承自object的new-style有效
## 好处：速度快；内存占用少
## 实现方式：直接把变量插入对象的内存布局中，访问直接按照*(instance memory address + attribute offset)
## 一般的instance attribute访问方式: 先通过偏移找到instance的__dict__，然后在这个哈希表中查找得到。为了防止哈希冲突大量发生，哈希表的容量比实际存储的值大(比如默认情况下Java HashMap的内存占用量是有效值的内存占用至少4/3倍)，而且时间复复杂度最坏O(n)


# Method Resoluation Order
## python 没有接口，必须支持类的多继承，为了确定子类对象的方法调用应该使用父类的哪一个方法，必须确定寻找方式
## class.__mro__ 或者class.mro()可以得到
## 只存在于继承自object的new-style


# descriptors
## 任何实现了 __get__, __set__ 和 __delete__中至少一个的类，其instance是一个descriptor
```
class Descr(object):
    def __get__(self, obj, owner):
        pass
    def __set__(self, obj, value):
        pass
    def __delete__(self, obj):
        pass

class Foo(object):
    name = Descr()

foo = Foo()
foo.name # 等价于 __get__(Foo.name, foo, Foo)
```

# meta class
## type 函数


# with .. as
## context manager， 类似于c#的using关键字和Java的try with resources语句
```
class Foo(object):
    def do_something(self):
        pass

class FooWrapper(object):
    def __enter__():
        return Foo()
    # type: type of exception raised
    # value: the instance of exception raised
    # traceback: traceback instance
    def __exit__(self, type, value, traceback):
        pass

with FooWrapper() as foo:
    foo.do_something()
```
## __enter__返回值赋值给as 后边的变量
## with..as语句块中的代码结束或者发生异常时，__exit__执行

# 