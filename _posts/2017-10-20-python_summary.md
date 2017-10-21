---
layout: post
title: Python summary
---

## slots
### 类变量__slots__被定义为一个可遍历类型(tuple, list..), 只有名字在其中的instance变量才能被定义
```
class Foo(object):
    __slots__ = ('name')

foo = Foo()
foo.name = 'foo'    # Right
foo.value = 'value' # AttributeError
```
### 只对继承自object的new-style有效
### 好处
- **速度快**
- **内存占用少**
### 实现方式
- 直接把变量插入对象的内存布局中，访问直接按照*(instance memory address + attribute offset)
### 一般的instance attribute访问方式:
- 先通过偏移找到instance的__dict__，然后在这个哈希表中查找得到。为了防止哈希冲突大量发生，哈希表的容量比实际存储的值大(比如默认情况下Java HashMap的内存占用量是有效值的内存占用至少4/3倍)，而且时间复复杂度最坏O(n)


---
## Method Resoluation Order
### python 没有接口，必须支持类的多继承，为了确定子类对象的方法调用应该使用父类的哪一个方法，必须确定寻找方式
### class.__mro__ 或者class.mro()可以得到
```
class C(object):
    pass

class D(object):
    pass

class A(C, D):
    def __init__(self):
        pass

class B(C, D):
    def __init__(self):
        pass

class Foo(A, B):
    pass

Foo.__mro__ # (Foo, A, B, C, D, object)
Foo.mro()   # the same as above
```
### 只存在于继承自object的new-style
### MRO 构造算法
1. 按照从左到右，从下到上的继承顺序，递归地得到类继承序列。比如上边的代码的继承序列是(Foo, A, C, D, B, C, D)
2. 在继承序列上依次剔除所有这样的异类：子类出现在继承序列上，并且在其之后。比如上边地继承序列中第一个C和D都是这样地类，需要删除。删除异类后的继承序列是(Foo, A, B, C, D)
3. 如果删除后的继承序列包含所有的类，那么它就是最终的MRO，否则构造失败。比如下面的代码就是不合法的：

```
class C(object):
    pass

class D(object):
    pass

class A(D, C):
    def __init__(self):
        pass

class B(C, D):
    def __init__(self):
        pass

class Foo(A, B):
    pas

Foo.__mro__ # (Foo, A, B, C, D, object)
Foo.mro()   # the same as above
```
因为最后得到的序列是(Foo, A, B)不包含C和D。运行时出现TypeError: Error when calling the metaclass bases Cannot create a consistent method resolution order (MRO) for bases D, C


---
## Context manager: with ... as
- 类似于c#的using关键字和Java的try-with-resources语句
- __enter__返回值赋值给as 后边的变量
- with..as语句块中的代码结束或者发生异常时，__exit__执行
```
class Foo(object):
    def do_something(self):
        pass

class FooWrapper(object):
    def __enter__():
        return Foo()
    def __exit__(self, type, value, traceback):
        print 'type of exception raised: {}'.format(type)
        print 'instance of exception raised: {}'.format(value)
        print 'traceback instance: {}'.format(traceback)

with FooWrapper() as foo:
    foo.do_something()
```


---
## Descriptors
### 任何实现了 __get__, __set__ 和 __delete__中至少一个的类，其instance是一个descriptor
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


---
## Meta class
### type 函数