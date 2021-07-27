---
title: "Python函数式编程"
subtitle: ""
date: 2021-06-03T15:59:44+08:00
lastmod: 2021-06-03T15:59:44+08:00
draft: true
author: "Createitv"
description: "Createitv"

page:
    theme: "classic"

upd: ""
authorComment: ""

tags: ["函数式编程"]
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



## 3种编程范式

### 命令式编程

Procedural programming is the most basic form of coding. Code is structured hierarchically into blocks (such as if statements, loops, and functions). It is arguably the simplest form of coding. However, it can be difficult to write and maintain large and complex software due to its lack of enforced structure.

```python
def add_values(any_list):
  sum = 0
  for x in any_list:
      sum += x
  return sum

my_list = [1,2,3,4]
print(add_values(my_list))
```

### 面向对象编程

Object-oriented programming (OOP) structures code into objects. An object typically represents a real item in the program, such as a file or a window on the screen, and it groups all the data and code associated with that item within a single software structure. Software is structured according to the relationships and interactions between different objects. Since objects are encapsulated, with well-defined behavior, and capable of being tested independently, it is much easier to write complex systems using OOP.

```python
class ListOperations(object):
  def __init__(self, any_list):
    self.any_list = any_list

  def add_values(self):
    self.sum = sum(self.any_list)

my_list = [1,2,3,4]
sum_values = ListOperations(my_list)
sum_values.add_values()

print(sum_values.sum)
```

### 函数式编程

Functional programming (FP) uses functions as the main building blocks. Unlike procedural programming, the functional paradigm treats functions as objects that can be passed as parameters, allowing new functions to be built dynamically as the program executes.

Functional programming tends to be more declarative than imperative – your code defines what you want to happen, rather than stating exactly how the code should do it. Some FP languages don’t even contain constructs, such as loops or if statements. However, Python is more general-purpose and allows you to mix programming styles very easily.

```python
import functools
my_list = [1, 2, 3, 4]

# We will look at the functools library later on
sum = functools.reduce(lambda x, y: x + y, my_list)
print(sum)
```

## 函数式编程特点

![函数式编程](https://tva1.sinaimg.cn/large/008i3skNgy1grares0jv6j31fm0qwwk5.jpg)

- 纯函数
- 没有副作用
- 函数是第一等公民
- 偏爱不可变对象
- 迭代器胜过不可迭代序列
- 惰性计算
- 避免循环和判断语句
- 递归代替循环
- 高阶函数

## 函数式编程的优缺点

### ![image-20210608125054924](https://typora-1300715298.cos.ap-shanghai.myqcloud.com/uPic/image-20210608125054924.png)

### 优点

- 代码更少
- 易读
- Bug更少，更好debug
- Code is potentially mathematically provable
- 易于并行计算多线程

## 缺点

![image-20210608125120112](https://typora-1300715298.cos.ap-shanghai.myqcloud.com/uPic/image-20210608125120112.png)

- 不可能完全纯函数
- 学习曲线高
- 效率较低，递归消耗大



