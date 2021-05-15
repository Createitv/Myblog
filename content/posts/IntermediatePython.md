---
title: "IntermediatePython"
subtitle: ""
date: 2021-05-15T22:22:05+08:00
lastmod: 2021-05-15T22:22:05+08:00
draft: true
author: "Createitv"
description: "Createitv"

page:
    theme: "classic"

upd: ""
authorComment: ""

tags: []
categories: ["python"]

hiddenFromHomePage: false
hiddenFromSearch: false

resources:
- name: "featured-image"
  src: "featured-image.jpg"
- name: featured-image-preview
  src: featured-image-preview.jpg

featuredImage: ""
featuredImagePreview: ""
images: [""]
---

## IntermediatePython

## `*args`和`**kwargs`

并不是必须写成`*args` 和`**kwargs`。 只有变量前面的 `*`(星号)才是必须的. 你也可以写成`*var` 和`**vars`. 而写成`*args` 和`**kwargs`只是一个通俗的命名约定。

#### `*args`的用法

`args`和`**kwargs`用于函数**不定数量**的参数传递，在函数使用者不知道会传递多少个参数下可使用

```python
def test_var_args(f_arg, *argv):
    print("first normal arg:", f_arg)
    for arg in argv:
        print("another arg through *argv:", arg)

>>>test_var_args('yasoob', 'python', 'eggs', 'test')
first normal arg: yasoob
another arg through *argv: python
another arg through *argv: eggs
another arg through *argv: test
```

#### `**kwargs`的用法

`**kwargs` 允许你将不定长度的**键值对**, 作为参数传递给一个函数。

```python
def greet_me(**kwargs):
	for key, value in kwargs.items():
		print("{0}=={1}".format(key, value))
>>>greet_me(name='yasoob')
name == yasoob
```

#### 使用`args`和`**kwargs`来调用函数

```python
def test_args_kwargs(arg1, arg2, arg3):
    print("arg1:", arg1)
    print('arg2:', arg2)
    print("arg3:", arg3)
# 首先使用*args
>>>args = ("two", 3, 5)
>>>test_args_kwargs(**kwargs)
arg1: two
arg2: 3
arg3: 5
#再使用**kwargs
>>> kwargs = {"arg3": 3, "arg2": "two", "arg1": 5}
>>> test_args_kwargs(**kwargs)
arg1: 5
arg2: two
arg3: 3
```

标准参数、缺省参数、`*args`、`**kwargs`在使用时候的顺序

```python
some_function(argument, argument=' ', *args, **kwargs )
```

#### 什么时候使用

根据需求而定：最常见在装饰起之中捕获修饰函数的不定参数

```python
def use_logging(func):
    def wrapper(*args, **kwargs): # Look this
        logging.warn("%s is running" % func.__name__)
        return func(*args)
    return wrapper
@use_logging
def foo():
    print("i am foo")
@use_logging
def bar():
    print("i am bar")
bar()
```

## 调试Debugging

>   利用好调试，能大大提高你捕捉代码Bug的。大部分新人忽略了Python debugger(`pdb`)的重要性。 

#### 从命令行启动

`$ python -m pdb my_script.py`

#### 从脚本内部启动

```python
import pdb

def make_bread():
    pdb.set_trace()  #设置断点
    return "I don't have time"

print(make_bread())
```

##### 命令列表：

-   `c`: 继续执行
-   `w`: 显示当前正在执行的代码行的上下文信息
-   `a`: 打印当前函数的参数列表
-   `s`: 执行当前代码行，并停在第一个能停的地方（相当于单步进入）
-   `n`: 继续执行到当前函数的下一行，或者当前行直接返回（单步跳过）

## 生成器Generators

迭代器(iterators)是一个让程序员可以遍历一个容器（特别是列表）的对象。然而，一个迭代器在遍历并读取一个容器的数据元素时，并不会执行一个迭代。

-   可迭代对象(Iterable)——形容词
-   迭代器(Iterator)——名词
-   迭代(Iteration)——动词

#### 可迭代对象(Iterable)

Python中任意的对象，只要它定义了可以返回一个迭代器的`__iter__`方法，或者定义了可以支持下标索引的`__getitem__`方法(这些双下划线方法会在其他章节中全面解释)，那么它就是一个可迭代对象。

#### 迭代器(Iterator)

任意对象，只要定义了`next`(Python2) 或者`__next__`方法，它就是一个迭代器。就这么简单。现在我们来理解迭代(iteration)

#### 迭代(Iteration)

从某个地方（比如一个列表）不断取出一个元素的过程。当我们使用一个循环来遍历某个东西时，这个过程本身就叫迭代。

#### 生成器(Generators)

生成器也是一种迭代器，一次只能迭代一次，类似版本迭代更新。在`python`中并没有直接把所有数据写入内存中，而是在运行时生成值。可以通过遍历来访问他们，大多数时候通过函数来实现。而且并不返回一个值而是yield一个值。

斐波那契数列生成器

```python
# generator version(减少资源消耗)
def fibon(n):
    a = b = 1
    for i in range(n):
        yield a
        a, b = b, a + b
for x in fibon(1000000):
    print(x)
    
#faction version
def fibon(n):
    a = b = 1
    result = []
    for i in range(n):
        result.append(a)
        a, b = b, a + b
    return result

```

## 高阶函数(Map，Filter，Reduce)

#### Map函数

`Map`会将一个函数映射到一个输入列表的所有元素上

`map(function_to_apply, list_of_inputs)`

```python
# 对比举例
items = [1, 2, 3, 4, 5]
squared = []
for i in items:
    squared.appedn(i**2)
# 等同于
items = [1, 2, 3, 4, 5]
squared = list(map(lambda x: x**2, items))
# 在python2中map直接返回列表，但在python3中返回迭代器
# 因此为了兼容python3, 需要list转换一下
```

Filter函数

`filter`过滤列表中的元素，并且返回一个由所有符合要求的元素所构成的列表，`符合要求`即函数映射到该元素时返回值为True

```python
number_list = range(-5, 5)less_than_zero = filter(lambda x:x <0, number_list)print(list(less_than_zero))
```

`filter`类似于一个`for`循环，但它是一个内置函数，并且更快。

注意：`map`和`filter`相对不那么优雅,推导式的可读性更好。

Reduce函数

**reduce()** 函数会对参数序列中元素进行累积

```python
reduce(function, iterable[, initializer]) #语法from functools import reduceproduct = reduce( (lambda x, y: x * y), [1, 2, 3, 4] )# Output: 24
```

## Set数据集合

`set`(集合)是一个非常有用的数据结构。它与列表(`list`)的行为类似，区别在于`set`不能包含重复的值。

```python
# 查看重复元素some_list = ['a', 'b', 'c', 'b', 'd', 'm', 'n', 'n']duplicates = []for value in some_list:    if some_list.count(value) > 1:        if value not in duplicates:            duplicates.append(value)print(duplicates)### 输出: ['b', 'n']
```

更简单更优雅的解决方案，那就是使用`集合(sets)`

```python
some_list = ['a', 'b', 'c', 'b', 'd', 'm', 'n', 'n']duplicates = set([x for x in some_list if some_list.count(x) > 1])print(duplicates)### 输出: set(['b', 'n'])
```

#### 交集

对比两个集合的交集（两个集合中都有的数据）

```python
valid = set(['yellow', 'red', 'blue', ' green', 'black'])input_set = set(['red', 'brown'])print(input_set.interserction(valid))### 输出:set(['red'])
```

#### 差集

用差集(difference)找出无效的数据，相当于用一个集合减去另一个集合的数据

```python
valid = set(['yellow', 'red', 'blue', 'green', 'black'])input_set = set(['red', 'brown'])print(input_set.difference(valid))### 输出:set(['brown'])
```

你也可以用`{}`符号来创建集合

```python
a_set = {'red', 'blue', 'green'}print(type(a_set))### 输出: <type 'set'>
```

## 三元运算符

三元运算符通常在Python里被称为条件表达式，这些表达式基于真(true)/假(false)的条件判断

**伪代码**

```python
#如果条件为真，返回真 否则返回假condition_is_true if condition else condition_is_false
```

**举例**

```python
number = 3flag = '单数' if number % 2 != 0 else '双数'print(flag)### 输出:单数
```

另外一种超级晦涩的语法

```python
#(返回假，返回真)[真或假](if_test_is_false, if_test_is_true)[test]fat = Truefitness = ("skinny", "fat")[fat]print("Ali is", fitness)#输出: Ali is fat
```

## 装饰器

修改其他函数的功能的函数。他们有助于让我们的代码更简短，也更Pythonic（Python范儿），新手跨越必备。

#### 函数也是对象

```python
def hi(name="yasoob"):    return "hi " + nameprint(hi())# output: 'hi yasoob'# 我们甚至可以将一个函数赋值给一个变量，比如greet = hi# 我们这里没有在使用小括号，因为我们并不是在调用hi函数# 而是在将它放在greet变量里头。我们尝试运行下这个print(greet())# output: 'hi yasoob'# 如果我们删掉旧的hi函数，看看会发生什么！del hiprint(hi())#outputs: NameErrorprint(greet())#outputs: 'hi yasoob'
```

#### 函数中定义函数

```pyhton
def hi(name="yasoob"):    print("now you are inside the hi() function")    def greet():        return "now you are in the greet() function"    def welcome():        return "now you are in the welcome() function"    print(greet())    print(welcome())    print("now you are back in the hi() function")hi()#output:now you are inside the hi() function#       now you are in the greet() function#       now you are in the welcome() function#       now you are back in the hi() function# 上面展示了无论何时你调用hi(), greet()和welcome()将会同时被调用。# 然后greet()和welcome()函数在hi()函数之外是不能访问的，比如：greet()#outputs: NameError: name 'greet' is not defined
```

从函数中返回函数

```python
def hi(name="yasoob"):    def greet():        return "now you are in the greet() function"    def welcome():        return "now you are in the welcome() function"    if name == "yasoob":        return greet    else:        return welcomea = hi()print(a)#outputs: <function greet at 0x7f2143c01500>#上面清晰地展示了`a`现在指向到hi()函数中的greet()函数#现在试试这个print(a())#outputs: now you are in the greet() function
```

#### 将函数作为参数传给另一个函数

```python
def hi():    return "hi yasoob!"def doSomethingBeforeHi(func):    print("I am doing some boring work before executing hi()")    print(func())doSomethingBeforeHi(hi)#outputs:I am doing some boring work before executing hi()#        hi yasoob!
```

#### 装饰器应用

```python
def a_new_decorator(a_func):    def wrapTheFunction():        print("I am doing some boring work before executing a_func()")        a_func()        print("I am doing some boring work after executing a_func()")    return wrapTheFunctiondef a_function_requiring_decoration():    print("I am the function which needs some decoration to remove my foul smell")a_function_requiring_decoration()#outputs: "I am the function which needs some decoration to remove my foul smell"a_function_requiring_decoration = a_new_decorator(a_function_requiring_decoration)#now a_function_requiring_decoration is wrapped by wrapTheFunction()a_function_requiring_decoration()#outputs:I am doing some boring work before executing a_func()#        I am the function which needs some decoration to remove my foul smell#        I am doing some boring work after executing a_func()
```

#### 装饰起语法糖

```python
@a_new_decoratordef a_function_requiring_decoration():    """Hey you! Decorate me!"""    print("I am the function which needs some decoration to "          "remove my foul smell")a_function_requiring_decoration()#outputs: I am doing some boring work before executing a_func()#         I am the function which needs some decoration to remove my foul smell#         I am doing some boring work after executing a_func()#the @a_new_decorator is just a short way of saying:a_function_requiring_decoration = a_new_decorator(a_function_requiring_decoration)
```

#### 使用场景

装饰器能有助于检查某个人是否被授权去使用一个web应用的端点(endpoint)。它们被大量使用于Flask和Django web框架中

```python
from functools import wrapsdef requires_auth(f):    @wraps(f)    def decorated(*args, **kwargs):        auth = request.authorization        if not auth or not check_auth(auth.username, auth.password):            authenticate()        return f(*args, **kwargs)    return decorated
```



## slots魔法

在Python中，每个类都有实例属性。默认情况下Python用一个字典来保存一个对象的实例属性。这非常有用，因为它允许我们在运行时去设置任意的新属性。

然而，对于有着已知属性的小类来说，它可能是个瓶颈。这个字典浪费了很多内存。Python不能在对象创建时直接分配一个固定量的内存来保存所有的属性。因此如果你创建许多对象（我指的是成千上万个），它会消耗掉很多内存。 不过还是有一个方法来规避这个问题。这个方法需要使用`__slots__`来告诉Python不要使用字典，而且只给一个固定集合的属性分配空间。

-   不使用`__slots__`:

```python
class MyClass(object):  def __init__(self, name, identifier):      self.name = name      self.identifier = identifier      self.set_up()  
```

-   使用`__slots__`

```python
class MyClass(object):  __slots__ = ['name', 'identifier']  def __init__(self, name, identifier):      self.name = name      self.identifier = identifier      self.set_up()
```

第二段代码会为你的内存减轻负担。通过这个技巧，有些人已经看到内存占用率几乎40%~50%的减少。

## 虚拟环境Virtualenv

#### 什么是虚拟环境？

`Virtualenv` 是一个工具，它能够帮我们创建一个独立(隔离)的Python环境。想象你有一个应用程序，依赖于版本为2的第三方模块，但另一个程序依赖的版本是3，请问你如何使用和开发这些应用程序？

如果你把一切都安装到了`/usr/lib/python2.7/site-packages`（或者其它平台的标准位置），那很容易出现某个模块被升级而你却不知道的情况。

在另一种情况下，想象你有一个已经开发完成的程序，但是你不想更新它所依赖的第三方模块版本；但你已经开始另一个程序，需要这些第三方模块的版本。

#### 虚拟环境安装

`pip install virtualenv`

#### 虚拟环境启动

`$ virtualenv myproject`
`$ source myproject/bin/activate`

执行第一个命令在`myproject`文件夹创建一个隔离的virtualenv环境，第二个命令激活这个隔离的环境(`virtualenv`)。

如果你想让你的`virtualenv`使用系统全局模块，请使用`--system-site-packages`

#### 虚拟环境关闭

`$ deactivate`

小工具

`smartcd`你可以使用`smartcd`来帮助你管理你的环境，当你切换目录时，它可以帮助你激活（activate）和退出（deactivate）你的`virtualenv`

## 容器(collections)

#### defaultdict

`defaultdict`与`dict`类型不同，你不需要检查**key**是否存在，所以能这样做

```python
from collections import defaultdictcolours = (    ('Yasoob', 'Yellow'),    ('Ali', 'Blue'),    ('Arham', 'Green'),    ('Ali', 'Black'),    ('Yasoob', 'Red'),    ('Ahmed', 'Silver'),)favourite_colours = defaultdict(list)for name, colour in colours:    favourite_colours[name].append(colour)print(favourite_colours)# defaultdict(<type 'list'>,#    {'Arham': ['Green'],#     'Yasoob': ['Yellow', 'Red'],#     'Ahmed': ['Silver'],#     'Ali': ['Blue', 'Black']# })
```

当你在一个字典中对一个键进行嵌套赋值时，如果这个键不存在，会触发`keyError`异常。 `defaultdict`允许我们用一个聪明的方式绕过这个问题。 首先我分享一个使用`dict`触发`KeyError`的例子，然后提供一个使用`defaultdict`的解决方案

```python
some_dict = {}some_dict['colours']['favourite'] = "yellow"## 异常输出：KeyError: 'colours'import collectionstree = lambda: collections.defaultdict(tree)some_dict = tree()some_dict['colours']['favourite'] = "yellow"## 运行正常
```

#### counter

`counter`是一个计数器，帮助我们对数据进行计数

```python
from collections import Countercolours = (    ('Yasoob', 'Yellow'),    ('Ali', 'Blue'),    ('Arham', 'Green'),    ('Ali', 'Black'),    ('Yasoob', 'Red'),    ('Ahmed', 'Silver'),)favs = Counter(name for name, colour in colours)print(favs)## 输出:## Counter({##     'Yasoob': 2,##     'Ali': 2,##     'Arham': 1,##     'Ahmed': 1##  })
```

我们也可以在利用它统计一个文件

```python
with open('filename', 'rb') as f:    line_count = Counter(f)print(line_count)
```

#### deque

`deque`提供了一个双端队列，你可以从头和尾两端添加或者删除元素。

```python
from collections import dequed = deque()d.append('1')d.append('2')d.append('3')print(len(d))# output:3print(d[0])# output:'1'print(d[-1])# output:'3'
```

从两端取出(pop)数据：

```python
d = deque(range(5))print(len(d))## 输出: 5d.popleft()## 输出: 0d.pop()## 输出: 4print(d)## 输出: deque([1, 2, 3])
```

#### namedtuple

#### enum.Enum

## 枚举Enumerate

枚举(`enumerate`)是Python内置函数

```python
my_list = ['apple', 'banana', 'grapes', 'pear']for c, value in enumerate(my_list, 1):    print(c, value)# 输出:(1, 'apple')(2, 'banana')(3, 'grapes')(4, 'pear')
```

## 对象自省

自省(introspection)，在计算机编程领域里，是指在运行时来判断一个对象的类型的能力。它是Python的强项之一。Python中所有一切都是一个对象，而且我们可以仔细勘察那些对象。Python还包含了许多内置函数和模块来帮助我们。

#### dir

返回一个列表，列出了一个对象所拥有的属性和方法。自省给了我们一个列表对象的所有方法的名字。当你没法回忆起一个方法的名字，这会非常有帮助。

```python
my_list = [1, 2, 3]dir(my_list)# Output: ['__add__', '__class__', '__contains__', '__delattr__', '__delitem__',# '__delslice__', '__doc__', '__eq__', '__format__', '__ge__', '__getattribute__',# '__getitem__', '__getslice__', '__gt__', '__hash__', '__iadd__', '__imul__',# '__init__', '__iter__', '__le__', '__len__', '__lt__', '__mul__', '__ne__',# '__new__', '__reduce__', '__reduce_ex__', '__repr__', '__reversed__', '__rmul__',# '__setattr__', '__setitem__', '__setslice__', '__sizeof__', '__str__',# '__subclasshook__', 'append', 'count', 'extend', 'index', 'insert', 'pop',# 'remove', 'reverse', 'sort']
```

#### type和id

`type`函数返回一个对象的类型

```python
print(type(''))# Output: <type 'str'>print(type([]))# Output: <type 'list'>print(type({}))# Output: <type 'dict'>print(type(dict))# Output: <type 'type'>print(type(3))# Output: <type 'int'>
```

`id()`函数返回任意不同种类对象的唯一ID

```python
name = "Yasoob"print(id(name))# Output: 139972439030304
```

#### inspect模块

`inspect`模块也提供了许多有用的函数，来获取活跃对象的信息。

```python
import inspectprint(inspect.getmembers(str))# Output: [('__add__', <slot wrapper '__add__' of ... ...
```

## 推导式

推导式（又称解析式）是Python的一种独有特性，推导式可以从一个数据序列构建另外一个数据序列的结构体。

-   列表推导式
-   字典推导式
-   集合推导式

#### List Comprehension

列表推导式（又称列表解析式）提供了一种简明扼要的方法来创建列表。 它的结构是在一个中括号里包含一个表达式，然后是一个`for`语句，然后是0个或多个`for`或者`if`语句。那个表达式可以是任意的，意思是你可以在列表中放入任意类型的对象。返回结果将是一个新的列表，在这个以`if`和`for`语句为上下文的表达式运行完成之后产生。

```python
variable = [out_exp for out_exp in input_list if out_exp == 2]
```

#### Dictionary Comprehension

```python
mcase = {'a': 10, 'b': 34, 'A': 7, 'Z': 3}mcase_frequency = {    k.lower(): mcase.get(k.lower(), 0) + mcase.get(k.upper(), 0)    for k in mcase.keys()}# mcase_frequency == {'a': 17, 'z': 3, 'b': 34}
```

快速对换一个字典的键和值

```python
{v: k for k, v in some_dict.items()}
```

#### Set Comprehension

它们跟列表推导式也是类似的。 唯一的区别在于它们使用大括号`{}`

```python
squared = {x**2 for x in [1, 1, 2]}print(squared)
```

## 异常

异常处理是一种艺术，一旦你掌握，会授予你无穷的力量

```python
try:    file = open('test.txt', 'rb')except IOError as e:    print("An IOError occurred. {}".format(e.args[-1]))
```

#### 处理多个异常

第一种方法需要把所有可能发生的异常放到一个元组里。像这样：



```python
try:    file = open('test.txt', 'rb')except (IOError, EOFError) as e:    print("An error occurred. {}".format(e.args[-1]))
```

另外一种方式是对每个单独的异常在单独的`except`语句块中处理。我们想要多少个`except`语句块都可以。

```python
try:    file = open('test.txt', 'rb')except EOFError as e:    print("An EOF error occurred.")    raise eexcept IOError as e:    print("An error occurred.")    raise e
```

##### 捕获所有异常

```python
try:    file = open("test.txt", 'rb')except Exception:    # 打印一些异常日志，如果你想要的话    raise
```

#### finally 从句

包裹到`finally`从句中的代码不管异常是否触发都将会被执行。这可以被用来在脚本执行之后做清理工作。

```python
try:    file = open("test.txt", 'rb')except IOError as e:    print("An IOError occurred.{}".format(e.args[-1]))finally:    print("This would be printed wheher or not an exception occurred!")
```

#### try / else从句

else可以用在不触发异常的情况下执行一些代码

```python
try:    print('I am sure no exception is going to occur!')except Exception:    print('exception')else:    # 这里的代码只会在try语句里没有触发异常时运行,    # 但是这里的异常将 *不会* 被捕获    print('This would only run if no exception occurs. And an error here '          'would NOT be caught.')finally:    print('This would be printed in every case.')# Output: I am sure no exception is going to occur!# This would only run if no exception occurs.# This would be printed in every case.
```

## lambda表达式

它们在其他语言中也被称为匿名函数。如果你不想在程序中对一个函数使用两次，你也许会想用lambda表达式

原型

```python
lambda 参数:操作(参数)
```

例子

```python
add = lambda x, y: x + yprint(add(3, 5))# Output: 8
```

列表排序

```python
a = [(1, 2), (4 ,1), (9, 10), (13, -3)]a.sort(key=lambda x: x[1])print(a)# Output: [(13, -3), (4, 1), (1, 2), (9, 10)]
```

## 一行式

#### **简易Web Server**

你是否想过通过网络快速共享文件？好消息，Python为你提供了这样的功能。

```python
# Python 2python -m SimpleHTTPServer# Python 3python -m http.server
```

#### **漂亮的打印**

你可以在Python REPL漂亮的打印出列表和字典。这里是相关的代码：

```python
from pprint import pprintmy_dict = {'name': 'Yasoob', 'age': 'undefined', 'personality': 'awesome'}    pprint(my_dict)
```

想快速漂亮的从文件打印出json数据，那么你可以这么做

```bash
cat file.json | python -m json.tool
```

#### **脚本性能分析** 

这可能在定位你的脚本中的性能瓶颈时

```python
python -m cProfile my_scripy.py
```

#### CSV转换为json

```bash
 python -c "import csv,json;print json.dumps(list(csv.reader(open('csv_file.csv'))))"
```

#### 一行式的构造器

```python
    class A(object):        def __init__(self, a, b, c, d, e, f):            self.__dict__.update({k: v for k, v in locals().items() if k != 'self'})
```

## For-else

else`从句会在循环正常结束时执行。这意味着，循环没有遇到任何`break

有个常见的构造是跑一个循环，并查找一个元素。如果这个元素被找到了，我们使用`break`来中断这个循环。有两个场景会让循环停下来。

-   第一个是当一个元素被找到，`break`被触发。
-   第二个场景是循环结束。

```python
for n in range(2, 10):    for x in range(2, n):        if n % x == 0:            print(n, 'equals', x, '*', n / x)           	break    else:        # loop fell through without finding a factor        print(n , 'is a prime number')
```

## 使用C扩展

开发者有三种方法可以在自己的Python代码中来调用C编写的函数-`ctypes`，`SWIG`，`Python/C API`。每种方式也都有各自的利弊。

要在Python中调用C？原因如下：

-   你要提升代码的运行速度，而且你知道C要比Python快50倍以上
-   C语言中有很多传统类库，而且有些正是你想要的，但你又不想用Python去重写它们
-   想对从内存到文件接口这样的底层资源进行访问
-   不需要理由，就是想这样做

#### Ctypes

Python中的[ctypes模块](https://docs.python.org/2/library/ctypes.html)可能是Python调用C方法中最简单的一种。ctypes模块提供了和C语言兼容的数据类型和函数来加载dll文件，因此在调用时不需对源文件做任何的修改。也正是如此奠定了这种方法的简单性。

#### SWIG

SWIG是Simplified Wrapper and Interface Generator的缩写。是Python中调用C代码的另一种方法。在这个方法中，开发人员必须编写一个额外的接口文件来作为SWIG(终端工具)的入口。

Python开发者一般不会采用这种方法，因为大多数情况它会带来不必要的复杂。而当你有一个C/C++代码库需要被多种语言调用时，这将是个非常不错的选择。

#### Python/C API

[Python/C API](https://docs.python.org/2/c-api/)可能是被最广泛使用的方法。它不仅简单，而且可以在C代码中操作你的Python对象。

## 函数缓存

函数缓存允许我们将一个函数对于给定参数的返回值缓存起来。 当一个I/O密集的函数被频繁使用相同的参数调用的时候，函数缓存可以节约时间。 在Python 3.2版本以前我们只有写一个自定义的实现。在Python 3.2以后版本，有个`lru_cache`的装饰器，允许我们将一个函数的返回值快速地缓存或取消缓存。

#### python3.2+

实现一个斐波那契计算器，并使用`lru_cache`

```python

```



