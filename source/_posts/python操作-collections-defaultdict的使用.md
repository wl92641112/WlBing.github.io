---
title: python操作-collections.defaultdict的使用
date: 2020-05-14 11:14:56
tags: [python]
categories: python工具方法
---

Python中通过Key访问字典，当Key不存在时，会引发‘KeyError’异常。为了避免这种情况的发生，可以使用collections类中的defaultdict()方法来为字典提供默认值。

语法格式：
collections.defaultdict([default_factory[, …]])

该函数返回一个类似字典的对象。defaultdict是Python内建字典类（dict）的一个子类，它重写了方法_missing_(key)，增加了一个可写的实例变量default_factory,实例变量default_factory被missing()方法使用，如果该变量存在，则用以初始化构造器，如果没有，则为None。其它的功能和dict一样。

第一个参数为default_factory属性提供初始值，默认为None；其余参数包括关键字参数（keyword arguments）的用法，和dict构造器用法一样。

1、使用list作第一个参数，可以很容易将键-值对序列转换为列表字典。

```python
from collections import defaultdict
s=[('yellow',1),('blue', 2), ('yellow', 3), ('blue', 4), ('red', 1)]
d=defaultdict(list)
for k, v in s:
    d[k].append(v)
a=sorted(d.items())
print(a)


[('blue', [2, 4]), ('red', [1]), ('yellow', [1, 3])]


```



当字典中没有的键第一次出现时，default_factory自动为其返回一个空列表，list.append()会将值添加进新列表；再次遇到相同的键时，list.append()将其它值再添加进该列表。

这种方法比使用dict.setdefault()更为便捷，dict.setdefault()也可以实现相同的功能。

```
s = [('yellow', 1), ('blue', 2), ('yellow', 3), ('blue', 4), ('red', 1)]
d={}
for k, v in s:
    d.setdefault(k,[]).append(v)
print('\n',d)
a=sorted(d.items())
print('\n',a)

# 输出
 {'red': [1], 'blue': [2, 4], 'yellow': [1, 3]}

 [('blue', [2, 4]), ('red', [1]), ('yellow', [1, 3])]
```



 2、defaultdict还可以被用来计数，将default_factory设为int即可。
代码如下： 

```python
from collections import defaultdict
s = 'mississippi'
d = defaultdict(int)
for k in s:
    d[k] += 1
print('\n',d)
a=sorted(d.items())
print('\n',a)

# 输出
 defaultdict(<class 'int'>, {'s': 4, 'p': 2, 'm': 1, 'i': 4})

 [('i', 4), ('m', 1), ('p', 2), ('s', 4)]
```

字符串中的字母第一次出现时，字典中没有该字母，default_factory函数调用int()为其提供一个默认值0,加法操作将计算出每个字母出现的次数。

函数int()是常值函数的一种特例，总是返回0。使用匿名函数（lambda function）可以更快、更灵活的创建常值函数，返回包括0在内的任意常数值。
代码：

```python
 from collections import defaultdict
def constant_factory(value):
    return lambda: value
d = defaultdict(constant_factory('<missing>'))
print('\n',d)
d.update(name='John', action='ran')
print('\n',d)
print('\n','%(name)s %(action)s to %(object)s' % d)


# 输出
 defaultdict(<function constant_factory.<locals>.<lambda> at 0x7ff29573aae8>, {})

 defaultdict(<function constant_factory.<locals>.<lambda> at 0x7ff29573aae8>, {'action': 'ran', 'name': 'John'})

 John ran to <missing>
```

 3、default_factory设为set时，可以用defaultdict建立集合字典（a dictionary of sets）。
代码： 

```python
 from collections import defaultdict
s = [('red', 1), ('blue', 2), ('red', 3), ('blue', 4), ('red', 1), ('blue', 4)]
d = defaultdict(set)
for k, v in s:
    d[k].add(v)
print('\n',d)
a=sorted(d.items())
print('\n',a)

# 输出
 defaultdict(<class 'set'>, {'red': {1, 3}, 'blue': {2, 4}})

 [('blue', {2, 4}), ('red', {1, 3})]
```



这个factory_function可以是list、set、str等等，作用是当key不存在时，返回的是工厂函数的默认值，比如list对应[ ]，str对应的是空字符串，set对应set( )，int对应0，如下举例：

```python
from collections import defaultdict

dict1 = defaultdict(int)
dict2 = defaultdict(set)
dict3 = defaultdict(str)
dict4 = defaultdict(list)
dict1[2] ='two'

print(dict1[1])
print(dict1[2])
print(dict2[1])
print(dict3[1])
print(dict4[1])
```



————————————————
版权声明：本文为CSDN博主「yangsong95」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/yangsong95/article/details/82319675