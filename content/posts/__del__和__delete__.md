---
title: "__Del__和__delete__"
subtitle: ""
date: 2021-09-02T17:02:25+08:00
lastmod: 2021-09-02T17:02:25+08:00
draft: true
author: "Createitv"
description: "Createitv"

page:
    theme: "classic"

upd: ""
authorComment: ""

tags: ["魔法函数"]
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



Both `__delete__` and `__del__` are [dunder or magic methods](https://www.geeksforgeeks.org/dunder-magic-methods-python/) in Python. Dunder or magic methods in Python are the methods having two prefix and suffix underscores in the method name. Dunder here means “Double Under (Underscores)”. These are commonly used for operator overloading.

#### __del__

`__del__` is a destructor method which is called as soon as all references of the object are deleted i.e when an object is garbage collected.

```python
import time
class Animal(object):
    # 创建完对象后会自动被调用
    def __init__(self, name):
        print('__init__方法被调用')
        self.__name = name
    # 当对象被删除时，会自动被调用
    def __del__(self):
        print("__del__方法被调用")
        print("%s对象马上被干掉了..." % self.__name)
# 创建对象
dog = Animal("哈皮狗")


-----------Output------------
>>> __init__方法被调用
>>> __del__方法被调用
>>> 哈皮狗对象马上被干掉了...
```

**Note :** The destructor was called after the program ended or when all the references to object are deleted i.e when the reference count becomes zero, not when object went out of scope.

#### __delete__

`__delete__` is used to delete the attribute of an instance i.e removing the value of attribute present in the owner class for an instance.

**Note:** This method only deletes the attribute which is a descriptor.

```python
class Example(object):

	# Initializing
	def __init__(self):
		print("Example Instance.")

	# Calling __delete__
	def __delete__(self, instance):
		print ("Deleted in Example object.")


# Creating object of Example
# class as an descriptor attribute
# of this class
class Foo(object):
	exp = Example()

# Driver's code
f = Foo()
del f.exp

----output----
>>> Example Instance.
>>> Deleted in Example object.
```

