## python一些细节

开始此文档目的是对python的细节进行补充，内容和知识点比较零散，更新时间不定。

### 1.1 默认参数

已知如下代码

```python
class Player:
    def __init__(self, name, items):
        self.name = name
        self.items = items
```

情形1:

当我实例化两个玩家时

```python
p1 = Player("alice", [])
p2 = Player("Bob", [])
```

会发现大部分玩家默认都没有任何道具。

此时，想到python重载机制，利用默认参数进行重载。

既然大部分玩家默认没有道具，能否定义一个默认参数对每一个玩家进行初始化呢？

```python
class Player:
    def __init__(self, name, items=[]):
        self.name = name
        self.items = items
```

这样，在进行实例化时，无需传入一个默认的道具参数。

```python 
p1 = Player("alice")
p2 = Player("bob")
p3 = Player("Charles", ["sword"])
```

问题出现，当玩家获取了道具时

```python
p1.items.append("bow")
p2.items.append("sword")

# 问题
print(p1.items)
OUT: ["bow", "sword"]
```

当定义默认参数时，只会evaluate一次值，也就是只求一次值。意味着两次初始化items用的是同一个list。

list 为mutable，所以二者共用了一个list

```python
print(id(self.items))
# 两次输出的id相同
```

解决方法: 将``items=[]``改为``items=None``

```python
class Player:
    def __init__(self, name, items=None):
        self.name = name
        if items is None:
        	self.items = []
        else:
            self.items = items
```

### 1.2 浮点数

如下代码:

```python
from objprint import op
op.config(arg_name=True)

op(0.1+0.2==0.3)
op(1e50==10**50)
op(1e500==1e600)
op(1e500>10**1000)
op(1e500*1e500>0)
op(1e500/1e500>0)
op(1e500/1e500==1e500/1e500)
```

输出

```
0.1+0.2==0.3:
False
1e50==10**50:
False
1e500==1e600:
True
1e500>10**1000:
True
1e500*1e500>0:
True
1e500/1e500>0:
False
1e500/1e500==1e500/1e500:
False
```

[关于浮点数的知识](http://zh.wikipedia.org/wiki/IEEE754)

计算机实现浮点数都使用IEEE754标准，由于浮点数的总体是无限且不可数的，我们不可能在计算机中完整地记录所有数字。

> IEEE754标准提供了二进制转浮点数的标准 具体内容[IEEE754](http://zh.wikipedia.org/wiki/IEEE754)

从而就产生了以上问题

问题1: 精度损失

```python
op(0.1+0.2==0.3)
0.1+0.2==0.3:
False
# python 中 1e50会被转为float而10**50会被当成一个big integer
# python中，整型是绝对精确的，浮点数不是。
# 故浮点数比较不应该使用==。而是比较两个数差值的绝对值是否小于一个比较小的值 0.00001
op(1e50==10**50)
1e50==10**50:
False
```

问题2: 

python使用64位双精度浮点数，其有最大的能表示的浮点数，对于双精度浮点数来说，这个数大概是10的308次方。

超出了这个数字：IEEE754提供了无穷( Inf )和非数值( NaN )两种表示形式。

```python
# 1e500超过了双精度浮点数最大能表示的范围，python将其转换成了Inf,1e600同理。
# 所以以下代码比较的是Inf 是否等于 Inf 详见IEEE754。
op(1e500==1e600)
1e500==1e600:
True
# 比较方式 -Inf = -Inf, Inf=Inf, NaN与任何浮点数（包括自身）的比较都为假。
# python在比较如下代码时如果这个float是Inf，则认为永远比integer要大。
op(1e500>10**1000)
1e500>10**1000:
True
# 既然如此 以下各数都是+Inf 正无穷乘以正无穷一定是大于0的。
# 但正无穷除以正无穷可能是大于等于零的任何值。
# 所以 Inf / Inf = NaN 不是数字 NaN当然不大于零。
op(1e500*1e500>0)
op(1e500/1e500>0)
1e500*1e500>0:
True
1e500/1e500>0:
False
# 左边是NaN右边也是NaN, 他们也不相等
op(1e500/1e500==1e500/1e500)
1e500/1e500==1e500/1e500:
False
```

综上，float可能会给程序带来意想不到的结果。

例如，在一些大型的数学计算中，中间的某一个过程出现了Inf或是NaN，他会随着计算将各种结果传染成Inf或NaN。

而且，程序不会报错，你无法意识到问题所在。

所以，要尽量避开浮点数。

如果不可避免，可使用以下方式检查关键步骤是否为Inf或NaN。

```python
import math
math.isinf(Var)
math.isnan(Var)
```

### 1.3 class里的函数如何变成方法的(self作用)

```python
class A:
    def f(self, data): # <-定义了两个位置参数
        print(data)
o = A()
o.f("hello") # <- 实际传入了一个 
# self保存了本身的对象
```

在定义class时会创建一个命名空间，运行class里的code block,将里面的产生的local variable赋值到``__dict__()``里面。

为什么只需要传入一个位置参数呢

```python
class A:
    def f(self, data): # <-定义了两个位置参数
        print(self.name)
        print(data)
o = A()

print(A.f)
# 只有实例化之后才只需传入一个值
print(o.f)
返回：
<function A.f at 0x7f7444387eb0>
<bound method A.f of <__main__.A object at 0x7f74443957e0>>
```

```python
class A:
    def f(self, data):
        print(data)
        print(self.name)
        
o = A()
o.name = "sam"

myfunc = o.f  # o.f实际上返回绑定在o本身上的一个方法 调用时不需要传入self
# 可以理解 f 在实例化时就已经改变，并不是最初定义的f
myfunc("hello")
返回
hello
sam
```

### 1.4 鸭子类型

>“当看到一只鸟走起来像鸭子、游泳起来像鸭子、叫起来也像鸭子，那么这只鸟就可以被称为鸭子。”

鸭子类型是动态类型的一种风格，在这个风格中一个对象有效的语义，不是由继承自特定的类或实现特定的接口，而是由“当前方法和属性的集合”决定

例如：

```python
# 可迭代对象
a = [11, 22, 33]
# 我们可以使用 for 循环来遍历他
for i in a:
    print(i)
# 现有自定义类
class B:
    pass
# 使用 for 循环必然会报错 此时 可以为类 B 添加一个 __iter__()方法
class B:
    def __iter__(self):
        return iter([1, 2, 3])
# 同理 我们可以使用len()函数来查看一个对象的长度
len(a)
# 如何使类B也能使用len函数查看长度呢
class B:
    def __len__(self):
        return 10
```

总结 python不会检查某个类是什么类型，他只会检查是否含有对应方法。

### 1.5 assert 关键字

assert 意为断言 当 后面的表达式为假时抛出__AssertionError__

例如

```python
x = "hello"

# 如果条件返回True，则什么也不会发生：
assert x == "hello"

#如果条件返回 False，则会引发 AssertionError：
assert x == "goodbye"
```

### 1.6 找到可迭代对象共同的接口

#### 常见的可迭代对象

```python
iterables = [
    "123",          # 字符串
    [1, 2, 3],      # 列表
    (1, 2, 3,),     # 元组
    {1:"a", 2:"b"}, # 字典
    {1, 2, 3}       # 集合
]
for iterable in iterables:
    print(type(iterable))
    for x in iterable:
        print(x, end='')
    print()
```



利用python 强大的类型能力

```python
def common_attrs(*objs):
    """计算对象之间的共同属性"""
    assert len(objs) > 0
    attrs = set(dir(objs[0]))
	for obj in objs[1:]:
        attrs &= set(dir(obj)) # 取交集
    attrs -= set(dir(object)) # 删除object 基础对象属性
    return attrs
iterable_common_attrs = common_attrs(*iterables)
print(iterable_common_attrs)
```

返回三个结果

```
__contains__ __len__ __iter__
```

这里列举的对象都是容器类型的可迭代对象

加入文件

```python
f = open("...", "r") # 文件也是可迭代对象
# 加入到可迭代对象清单中
iterables.append(f)
iterable_common_attrs &= set(dir(f))
print(iterable_common_attrs)
```

返回

```
__iter__
```

对于双下划线的魔法方法 `__iter__()`，对应的调用方法就是内置函数iter()

对所有的可迭代对象调用`iter()`方法

```python
for iterable in iterables:
    print(iter(iterable))
```

```
<str_iterator object at 0x7f07f2c3c700>
<list_iterator object at 0x7f07f2c3c700>
<tuple_iterator object at 0x7f07f2c3c700>
<dict_keyiterator object at 0x7f07f2a520c0>
<set_iterator object at 0x7f07f2a56640>
<_io.TextIOWrapper name='test.ui' mode='r' encoding='UTF-8'>
```

此外 由可迭代对象列表得到相应的迭代器列表

```python
iterators = [iter(iterable) for iterable in iterables]
iterator_common_attrs = common_attrs(*iterators)
print(iterator_common_attrs)
```

```
{'__next__', '__iter__'}
```

所以 迭代器有两个共同接口 `__iter__` 和 `__next__` 迭代器也是一个可迭代对象

#### 关于next魔法方法

![image-20220528140933468](python%20%E5%BA%95%E5%B1%82%E5%85%A5%E9%97%A8.assets/image-20220528140933468.png)

至此 我们了解到迭代的三个步骤：

* 调用 `iter(iterable)`来构建迭代器
* （多次）调用`next(iterator)`来获取值
* 最后捕获`StopIteration`异常来判断迭代结束

现在 我们可以使用`while循环`来模拟`for循环`迭代

```python
# 创建迭代器
iterator = iter(actions) # 对应可迭代对象的 __iter__ 方法
while 1:
    try:
        # 通过迭代器获取下一个对象
        print(next(iterator)) # 对应迭代器的 __next__ 方法
        except StopIteration:
            # 迭代结束 跳出循环
            break
```

迭代器的`__iter__`方法作用是什么呢？

#### 了解如何定义一个迭代器

迭代器的基本功能：

* 初始化时要传入可迭代对象，这样才能知道去哪里取数据
* 要初始化迭代进度
* 每次迭代调用`__iter__()`方法时：
  * 如果仍有元素可以迭代，则返回本轮迭代的元素，同时更新当前迭代进度
    * 如果无元素可以迭代，则迭代结束，抛出`StopIteration`异常

看起来没什么作用，所以我们可以给迭代器添加额外的功能

* 设置一个黑名单，如果当前元素在黑名单内，则跳过
* 将某些符合条件的数据`*2`后返回

```python
BLACK_LIST = ["白嫖"，"取关"]

class SuzhiIterator:
    def __init__(self, actions):
        self.actions = actions
        self.index = 0

    def __next__(self):
        while self.index < len(self.actions):
            action = self.actions[self.index]
            self.index += 1
            if action in BLACK_LIST:
                continue
            elif '硬币' in action:
                return action * 2
            else:
                return action
        raise StopIteration

actions = ['点赞','投币','取关']
sz_iterator = SuzhiIterator(actions)
while True:
    try:
        print(next(sz_iterator))
    except StopIteration:
        break
```

以上代码运行起来沒什么问题

但当我们使用`for 循环`来遍历时 会报错

```python
# for 循环需要的是 可迭代对象 而不是迭代器
for x in SuzhiIterator(actions):
    print(x)
    
TypeError: 'SuzhiIterator' object is not iterable
```

要想这个对象是`iterable` 那么你需要实现`__iter__`接口

为了构建这个可迭代对象 可以再新建一个类

```python
class SuzhiActions:
    def __init__(self, actions):
        self.actions = actions
    def __iter__(self):
        return SuzhiIterator(self.actions)
for x in SuzhiActions(actions):
    print(x)
```

当然 这样做 显然有点多余 因为他只完成了构建可迭代对象的过程

通常这样 让迭代器自己来实现`__iter__`接口

```python
...# __iter__ 接口就是要返回一个迭代器
def __iter__(self):
    return self
```

现在 迭代器就可以在`for 循环`中使用了

```python
# 迭代器 同时 也是 可迭代对象
```

#### 迭代器协议

在python文档中明确指出了，迭代器对象必须同时实现`__next__`和`__iter__`两个方法，这称为**迭代器协议**。

根据这个协议，迭代器**必须**是可迭代的，换言之，**迭代器 是一种 可迭代对象 **。

故缺少了`__iter__`方法的迭代器是不完整的，不符合迭代器协议的要求。

所有迭代器的`__iter__`方法大多数都只要`return self`即可。

#### 迭代器的意义

浅层的意义

* 统一通过`next()`方法获取数据，可以屏蔽底层不同的数据读取方式，从而简化编程。
* 容器类的数据结构只关心数据的金泰存储，每一次迭代都需要额外的迭代器对象专门负责记录迭代过程中的状态信息。

按照这个思路 很容易形成这样的认知：迭代器就是为了让数据能够更快捷地遍历 从而定义的辅助对象

```
这一些迭代对象对我们来说非常陌生，不会去使用他们，所以这种认知在这个层面上来讲，是没有错误的。
<str_iterator object at 0x7f07f2c3c700>
<list_iterator object at 0x7f07f2c3c700>
<tuple_iterator object at 0x7f07f2c3c700>
<dict_keyiterator object at 0x7f07f2a520c0>
<set_iterator object at 0x7f07f2a56640>
<_io.TextIOWrapper name='test.ui' mode='r' encoding='UTF-8'>
```

迭代器的作用并不止于此。

如果只是在`for 循环`中使用，是无需用到迭代器的`__iter__`方法的，就像前面使用`while循环`来模拟`for循环`。

深层意义

迭代器的`__iter__`方法是点睛之笔！

对此，我们分一下类

现在有两种可迭代对象

* 容器类型的
  * 列表、元组、字典等
  * 只有`__iter__`接口
  * 静态的数据
  * 需要额外的迭代器支持
  * 支持多次迭代

* 迭代器类型
  * 文件、 StringIO等
  * 同时实现`__iter__`和`__next__`接口
  * 动态的
  * 只能迭代一次

也就是说

```python 
# 针对容器类型可迭代对象的循环
for x in iterable: # 背后： 将可迭代对象 生成了 迭代器
    pass # 迭代器的生命周期和此循环绑定在一起
```

而一旦迭代器实现了`__iter__`方法

首先迭代器的构建就脱离的这个循环的存在，整个迭代过程只需要迭代器就够了

从此 迭代器不光是从后台走向了前台，而且直接让可迭代对象原理了循环

迭代器的构建是在代码中单独完成的，和当前循环操作**解耦**了

于是：

* 一个可迭代对象可以构建出任意多个不同的迭代器
* 一种迭代器可以应用有v任意多个可迭代对象（包括其他迭代器）

#### 应用场景

1. 构建一个数据管道

如果迭代器不可迭代：

三者关系如下

 [for循环] <- 迭代器（隐藏） <- 可迭代对象

如果迭代器可以任意嵌套连接的：

for 循环 <- 迭代器 <-迭代器 <- 迭代器 <- 迭代器*n <- 可迭代对象

* 很多的迭代器串联起来，形成一个处理数据v的管道，或者称为数据流
* 在这个管道中，每一次只通过一份数据v,避免了一次性加载所有数据
* 迭代器也不仅仅只是按返回数据那么简单了，他开始承担处理数据的责任
  * 例如， `SuzhiIterator`实现了部分过滤器和放大器的功能

* 当通过迭代器获取数据的时候，远离了数据存储，渐渐开始不关心数据如何存储。

2. 数据生成器

```python
from random import random

class Random:
    def __iter__(self):
        return self
    def __next__(self):
        return random()
```

这个迭代器不但不需要存储数据，甚至连`StopIteration`都不需要捕获

它可以无穷地迭代下去，每次数据都是实时产生的，不占用内存

（这不就是生成器吗！——）

虽然该迭代器是名副其实的数据生成器，但是生成器在python 中特指包含`yield` 的函数对象，以后会记录。

附:没有定义`__iter__`的可迭代对象

并不是所有的可迭代对象都必须定义`__iter__`方法

如果一个对象没有`__iter__`方法，但是定义了`__getitem__`方法，同样是可迭代的。

```python
# 例如 
class DumbList:
    def __getitem__(self, index):
        if index > 5:
            raise StopIteration
        return index * 2
dl = DumbList()
for x in dl:
    print(x)
```

因此，不能通过检查存在`__iter__` 方法来判断一个对象是否为可迭代的，而是应该直接使用`iter()`函数，如果不可迭代，则会抛出`TypeError`异常。

```python
class NotIterable:
    pass
ni = NotIterable()
it = iter(ni)
```
