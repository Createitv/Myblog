---
title: "Python异步性能对比"
subtitle: ""
date: 2021-09-04T14:35:24+08:00
lastmod: 2021-09-04T14:35:24+08:00
draft: true
author: "Createitv"
description: "Createitv"

page:
    theme: "classic"

upd: ""
authorComment: ""

tags: ["异步编程"]
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

## 为什么出现异步编程

通常来说，程序都是顺序执行，同一时刻只会发生一件事。如果一个函数依赖于另一个函数的结果，它只能等待那个函数结束才能继续执行，从用户的角度来说，整个程序才算运行完毕.

Mac 用户有时会经历过这种旋转的彩虹光标（常称为沙滩球），操作系统通过这个光标告诉用户：“现在运行的程序正在等待其他的某一件事情完成，才能继续运行，都这么长的时间了，你一定在担心到底发生了什么事情”。

这是令人沮丧的体验，没有充分利用计算机的计算能力 — 尤其是在计算机普遍都有多核CPU的时代，坐在那里等待毫无意义，你完全可以在另一个处理器内核上干其他的工作，同时计算机完成耗时任务的时候通知你。这样你可以同时完成其他工作，这就是**异步编程**的出发点。你正在使用的编程环境（就web开发而言，编程环境就是web浏览器）负责为你提供异步运行此类任务的API。

异步技术非常有用，特别是在web编程。当浏览器里面的一个web应用进行密集运算还没有把控制权返回给浏览器的时候，整个浏览器就像冻僵了一样，这叫做**阻塞；**这时候浏览器无法继续处理用户的输入并执行其他任务，直到web应用交回处理器的控制。

### CPU的时间观

![image-20210904091940469](https://typora-1300715298.cos.ap-shanghai.myqcloud.com/img/image-20210904091940469.png)


我们将一个 2.6GHz 的 CPU 拟人化，假设它执行一条命令的时间，他它感觉上过了一秒钟。CPU是计算机的处理核心，也是最宝贵的资源，如果有浪费CPU的运行时间，导致其利用率不足，那程序效率必然低下（因为实际上有资源可以使效率更高）。

如上图所示，在千兆网上传输2KB数据，CPU感觉过了14个小时，如果是在10M的公网上呢？那效率会低百倍！如果在这么长的一段时间内，CPU只是傻等结果而不能去干其他事情，是不是在浪费CPU的青春？

浪费“CPU”的时间等于谋财害命。而凶手就是程序猿。

### 面临的问题

#### 成本问题

如果一个程序不能有效利用一台计算机资源，那必然需要更多的计算机通过运行更多的程序实例来弥补需求缺口。例如我前不久主导重写的项目，使用Python异步编程，改版后由原来的7台服务器削减至3台，成本骤降57%。一台AWS m4.xlarge 型通用服务器按需付费实例一年价格约 1.2 万人民币。

#### 效率问题

如果不在乎钱的消耗，那也会在意效率问题。当服务器数量堆叠到一定规模后，如果不改进软件架构和实现，加机器是徒劳，而且运维成本会骤然增加。比如别人家的电商平台支持6000单/秒支付，而自家在下单量才支撑2000单/秒，在双十一这种活动的时候，钱送上门也赚不到。

#### C10k/C10M挑战

C10k（concurrently handling 10k connections）是一个在1999年被提出来的技术挑战，如何在一颗1GHz CPU，2G内存，1gbps网络环境下，让单台服务器同时为1万个客户端提供FTP服务。而到了2010年后，随着硬件技术的发展，这个问题被延伸为C10M，即如何利用8核心CPU，64G内存，在10gbps的网络上保持1000万并发连接，或是每秒钟处理100万的连接。（两种类型的计算机资源在各自的时代都约为1200美元）

成本和效率问题是从企业经营角度讲，C10k/C10M问题则是从技术角度出发挑战软硬件极限。C10k/C10M 问题得解，成本问题和效率问题迎刃而解。

## 什么是异步编程

### **阻塞**

-   程序未得到所需计算资源时被挂起的状态。
-   **程序在等待某个操作完成期间，自身无法继续干别的事情，则称该程序在该操作上是阻塞的**。
-   常见的阻塞形式有：网络I/O阻塞、磁盘I/O阻塞、用户输入阻塞等。

阻塞是无处不在的，包括CPU切换上下文时，所有的进程都无法真正干事情，它们也会被阻塞。（如果是多核CPU则正在执行上下文切换操作的核不可被利用。）

### **非阻塞**

-   **程序在等待某操作过程中，自身不被阻塞，可以继续运行干别的事情，则称该程序在该操作上是非阻塞的**。
-   非阻塞并**不是**在任何程序级别、任何情况下都可以存在的。
-   仅当程序封装的级别可以囊括独立的子程序单元时，它才可能存在非阻塞状态。

非阻塞的存在是因为阻塞存在，正因为某个操作阻塞导致的耗时与效率低下，我们才要把它变成非阻塞的。

### **同步**

-   不同程序单元为了完成某个任务，在执行过程中需靠某种通信方式以**协调一致**，称这些程序单元是同步执行的。
-   例如购物系统中更新商品库存，需要用“行锁”作为通信信号，让不同的更新请求强制排队顺序执行，那更新库存的操作是同步的。
-   简言之，**同步意味着有序**

### **异步**

-   为完成某个任务，不同程序单元之间**过程中无需通信协调**，也能完成任务的方式。
-   不相关的程序单元之间可以是异步的。
-   例如，爬虫下载网页。调度程序调用下载程序后，即可调度其他任务，而无需与该下载任务保持通信以协调行为。不同网页的下载、保存等操作都是无关的，也无需相互通知协调。这些异步操作的完成时刻并不确定。
-   简言之，**异步意味着无序**。

上文提到的“通信方式”通常是指异步和并发编程提供的同步原语，如信号量、锁、同步队列等等。我们需知道，虽然这些通信方式是为了让多个程序在一定条件下同步执行，但正因为是异步的存在，才需要这些通信方式。如果所有程序都是按序执行，其本身就是同步的，又何需这些同步信号呢？

### **并发**

-   并发描述的是程序的组织结构。指程序要被设计成多个可独立执行的子任务。
-   **以利用有限的计算机资源使多个任务可以被实时或近实时执行为目的**

### **并行**

-   并行描述的是程序的执行状态。指多个任务同时被执行。
-   **以利用富余计算资源（多核CPU）加速完成多个任务为目的**

并发提供了一种程序组织结构方式，让问题的解决方案可以并行执行，但并行执行不是必须的

### **概念总结**

-   **并行**是为了利用多核加速多任务完成的进度
-   **并发**是为了让独立的子任务都有机会被尽快执行，但不一定能加速整体进度
-   **非阻塞**是为了提高程序整体执行效率
-   **异步**是高效地组织非阻塞任务的方式

要支持并发，必须拆分为多任务，不同任务相对而言才有阻塞/非阻塞、同步/异步。所以，并发、异步、非阻塞三个词总是如影随形

### **异步编程**

-   **以进程、线程、协程、函数/方法作为执行任务程序的基本单位，结合回调、事件循环、信号量等机制，以提高程序整体执行效率和并发能力的编程方式**

如果在某程序的运行时，能根据已经执行的指令准确判断它接下来要进行哪个具体操作，那它是同步程序，反之则为异步程序。（无序与有序的区别）

同步/异步、阻塞/非阻塞并非水火不容，要看讨论的程序所处的封装级别。例如购物程序在处理多个用户的浏览请求可以是异步的，而更新库存时必须是同步的

### **GIL 全局解释器锁**

如何解决线程安全问题？CPython 解释器使用了加锁的方法。每个进程有一把锁，启动线程先加锁，结束线程释放锁。打个比方，进程是一个厂房，厂房大门是开着的，门内有锁，工人进入大门后可以在内部上锁。厂房里面有 10 个车间对应 10 个线程，每个 CPU 就是一个工人。GIL（Global Interpreter Lock）全局锁就相当于厂房规定：工人要到车间工作，从厂房大门进去后要在里面反锁，完成工作后开锁出门，下一个工人再进门上锁。也就是说，任意时刻厂房里只能有一个工人，但这样就保证了工作的安全性，这就是 GIL 的原理。当然了，GIL 的存在有很多其它益处，包括简化 CPython 解释器和大量扩展的实现

## python异步编程历史

历史上，Python 并不支持专门的异步编程语法，因为不需要。

有了[多线程（`threading`）和多进程（`multiprocessing`）](https://timber.io/blog/multiprocessing-vs-multithreading-in-python-what-you-need-to-know/)，就没必要一定支持异步了。如果一个线程（或进程）阻塞，新建其他线程（或进程）就可以了，程序不会卡死。

![img](https://www.wangbase.com/blogimg/asset/201911/bg2019112002.jpg)

但是，多线程有"线程竞争"的问题，处理起来很复杂，还涉及加锁。对于简单的异步任务来说，写起来很麻烦。

![img](https://www.wangbase.com/blogimg/asset/201911/bg2019112003.jpg)

Python 3.4 引入了 `asyncio` 模块，增加了异步编程，跟 JavaScript 的`async/await` 极为类似，大大方便了异步任务的处理。它受到了开发者的欢迎，成为从 Python 2 升级到 Python 3 的主要理由之一。

### python协程发展过程

多线程->多进程-> 非阻塞回调->事件循环->(Co-routine)协程->`yield from 协程函数`[python3.4]>**asyncio**语法`async await` ->Python 3.6 中**asyncio**库“转正”

### asyncio和原生协程初体验

```PYTHON
import asyncio


async def fun1():
    print(1)
    await asyncio.sleep(2)         # 模拟IO操作
    print(2)


async def fun2():
    print(3)
    await asyncio.sleep(2)         # 模拟IO操作
    print(4)
```

#### 运行结果

![IO阻塞](https://typora-1300715298.cos.ap-shanghai.myqcloud.com/img/IO阻塞.gif)

 可以看到遇到IO阻塞自动切换线程,两步各需要2s的IO阻塞也一起执行。

`asyncio `模块在单线程上启动一个事件循环（event loop），时刻监听新进入循环的事件，加以处理，并不断重复这个过程，直到异步任务结束。事件循环的内部机制

![img](https://www.wangbase.com/blogimg/asset/201911/bg2019112005.jpg)

## asyncio API

 `asyncio` 模块最主要的几个API。注意，必须使用 Python 3.7 或更高版本，早期的语法已经变了。

第一步，`import` 加载 `asyncio` 模块。



```python
import asyncio
```



第二步，函数前面加上 `async` 关键字，就变成了 async 函数。这种函数最大特点是执行可以暂停，交出执行权。

```python
async def main():
```

第三步，在 async 函数内部的异步任务前面，加上`await`命令。

```python
await asyncio.sleep(1)
```

上面代码中，`asyncio.sleep(1)` 方法可以生成一个异步任务，休眠1秒钟然后结束。

执行引擎遇到`await`命令，就会在异步任务开始执行之后，暂停当前 async 函数的执行，把执行权交给其他任务。等到异步任务结束，再把执行权交回 async 函数，继续往下执行。

第四步，`async.run()` 方法加载 async 函数，启动事件循环。

```python
asyncio.run(main())
```

上面代码中，`asyncio.run()` 在事件循环上监听 async 函数`main`的执行。等到 `main` 执行完了，事件循环才会终止。

## `asyncio`实例

先用`flask`开启一个模拟环境，代码如下

```PYTHON
# -*- coding: utf-8 -*-
from flask import Flask
import time
app = Flask(__name__)

@app.route('/')
def index():
    # 控制响应时间10s之后才响应
    time.sleep(4)
    return "<h3>慢慢倒数4s<h3>"

if __name__ == '__main__':
    app.run(debug=True)
```

### 单线程版

```python
# -*- coding: utf-8 -*-
import requests
import time

start = time.time()
URL = 'http://127.0.0.1:5000'

def get(URL):
    r = requests.get(URL)
    print(r.status_code, r.url, r.text)

if __name__ == '__main__':
    for _ in range(10):
        get(URL)
    end = time.time()
    print("duration time:", end - start)
    
运行结果
200 http://127.0.0.1:5000/ <h3>慢慢倒数1s<h3>
200 http://127.0.0.1:5000/ <h3>慢慢倒数1s<h3>
200 http://127.0.0.1:5000/ <h3>慢慢倒数1s<h3>
200 http://127.0.0.1:5000/ <h3>慢慢倒数1s<h3>
200 http://127.0.0.1:5000/ <h3>慢慢倒数1s<h3>
200 http://127.0.0.1:5000/ <h3>慢慢倒数1s<h3>
200 http://127.0.0.1:5000/ <h3>慢慢倒数1s<h3>
200 http://127.0.0.1:5000/ <h3>慢慢倒数1s<h3>
200 http://127.0.0.1:5000/ <h3>慢慢倒数1s<h3>
200 http://127.0.0.1:5000/ <h3>慢慢倒数1s<h3>
duration time: 40.12979030609131
```

单线程耗时40.1s

### 多线程版

```python
# -*- coding: utf-8 -*-
import requests
import time
import concurrent.futures

start = time.time()
URL = 'http://127.0.0.1:5000'


def get(URL):
    r = requests.get(URL)
    print(r.status_code, r.url, r.text)


if __name__ == '__main__':
    pool = concurrent.futures.ThreadPoolExecutor(max_workers=6)
    for result in pool.map(get, [URL for _ in range(10)]):
        print(result)
    end = time.time()
    print("duration time:", end - start)


运行结果
(200, 'http://127.0.0.1:5000/', '<h3>慢慢倒数1s<h3>')
(200, 'http://127.0.0.1:5000/', '<h3>慢慢倒数1s<h3>')
(200, 'http://127.0.0.1:5000/', '<h3>慢慢倒数1s<h3>')
(200, 'http://127.0.0.1:5000/', '<h3>慢慢倒数1s<h3>')
(200, 'http://127.0.0.1:5000/', '<h3>慢慢倒数1s<h3>')
(200, 'http://127.0.0.1:5000/', '<h3>慢慢倒数1s<h3>')
(200, 'http://127.0.0.1:5000/', '<h3>慢慢倒数1s<h3>')
(200, 'http://127.0.0.1:5000/', '<h3>慢慢倒数1s<h3>')
(200, 'http://127.0.0.1:5000/', '<h3>慢慢倒数1s<h3>')
(200, 'http://127.0.0.1:5000/', '<h3>慢慢倒数1s<h3>')
duration time: 8.07605242729187
```

多线程耗时8.07S

### 多进程版

```python
# -*- coding: utf-8 -*-
import requests
import time
import concurrent.futures

start = time.time()
URL = 'http://127.0.0.1:5000'


def get(URL):
    r = requests.get(URL)
    return r.status_code, r.url, r.text


if __name__ == '__main__':
    pool = concurrent.futures.ProcessPoolExecutor(max_workers=6)
    for result in pool.map(get, [URL for _ in range(10)]):
        # do something with result
        print(result)
    end = time.time()
    print("duration time:", end - start)

# 运行结果
(200, 'http://127.0.0.1:5000/', '<h3>慢慢倒数1s<h3>')
(200, 'http://127.0.0.1:5000/', '<h3>慢慢倒数1s<h3>')
(200, 'http://127.0.0.1:5000/', '<h3>慢慢倒数1s<h3>')
(200, 'http://127.0.0.1:5000/', '<h3>慢慢倒数1s<h3>')
(200, 'http://127.0.0.1:5000/', '<h3>慢慢倒数1s<h3>')
(200, 'http://127.0.0.1:5000/', '<h3>慢慢倒数1s<h3>')
(200, 'http://127.0.0.1:5000/', '<h3>慢慢倒数1s<h3>')
(200, 'http://127.0.0.1:5000/', '<h3>慢慢倒数1s<h3>')
(200, 'http://127.0.0.1:5000/', '<h3>慢慢倒数1s<h3>')
duration time: 8.361502647399902
```

![多进程](https://typora-1300715298.cos.ap-shanghai.myqcloud.com/img/多进程.gif)

`max_workers=6`，没有资源竞争，用window10跑竟然比多线程还要快，多进程整个用时8.4s

### 异步IO版

```python
import aiohttp
import asyncio
import time

start = time.time()

URL = "http://127.0.0.1:5000/"
session = None
async def fetch():
    async with session.get(URL) as response:
        print(await response.text(), response.url, response.status)

async def main():
    global session
    session = aiohttp.ClientSession()
    scrape_index_tasks = [asyncio.ensure_future(fetch()) for _ in range(10)]
    await asyncio.gather(*scrape_index_tasks)


if __name__ == '__main__':
    loop = asyncio.get_event_loop()
    loop.run_until_complete(main())
    print("End time", time.time()-start)
    
# 运行结果
<h3>慢慢倒数1s<h3> http://127.0.0.1:5000/ 200
<h3>慢慢倒数1s<h3> http://127.0.0.1:5000/ 200
<h3>慢慢倒数1s<h3> http://127.0.0.1:5000/ 200
<h3>慢慢倒数1s<h3> http://127.0.0.1:5000/ 200
<h3>慢慢倒数1s<h3> http://127.0.0.1:5000/ 200
<h3>慢慢倒数1s<h3> http://127.0.0.1:5000/ 200
<h3>慢慢倒数1s<h3> http://127.0.0.1:5000/ 200
<h3>慢慢倒数1s<h3> http://127.0.0.1:5000/ 200
<h3>慢慢倒数1s<h3> http://127.0.0.1:5000/ 200
<h3>慢慢倒数1s<h3> http://127.0.0.1:5000/ 200
End time 4.025025129318237
```

![异步IO2](https://typora-1300715298.cos.ap-shanghai.myqcloud.com/img/异步IO2.gif)

### 整个用时对比

代码如下

```python
# -*- coding: utf-8 -*-
import requests
import time
import concurrent.futures
import matplotlib.pyplot as plt
import aiohttp
import asyncio

start = time.time()
URL = 'http://127.0.0.1:5000' # 5S之后返回结果


def time_wrapper(func):  # 装饰器函数，func为被装饰函数
    def inner(*args, **kwargs):
        """被装饰函数前需要添加的内容"""
        start = time.time()
        name = func.__name__
        ret = func(*args, **kwargs)  # 被装饰函数
        """被装饰函数后需要添加的内容"""
        end = time.time()
        duration = round((end - start), 2)
        print(f"{name} 请求次数{i} 所需用时: {duration} s")
        return ret

    return inner

def get(URL):
    r = requests.get(URL)
    return r.status_code, r.url, r.text

@time_wrapper
def single_thread(i):
    for _ in range(i):
        get(URL)


@time_wrapper
def multithrad(i):
    pool = concurrent.futures.ThreadPoolExecutor(max_workers=20)
    for result in pool.map(get, [URL for _ in range(i)]):
        # print(result)
        time.sleep(0)

@time_wrapper
def multiprocess(i):
    pool = concurrent.futures.ProcessPoolExecutor(max_workers=20)
    for result in pool.map(get, [URL for _ in range(i)]):
        # print(result)
        time.sleep(0)

async def fetch():
    async with session.get(URL) as response:
        await response.text(), response.url, response.status

async def main(i):
    global session
    session = aiohttp.ClientSession()
    scrape_index_tasks = [asyncio.ensure_future(fetch()) for _ in range(i)]
    await asyncio.gather(*scrape_index_tasks)

@time_wrapper
def async_get(i):
    loop = asyncio.get_event_loop()
    loop.run_until_complete(main(i))


if __name__ == '__main__':
    process_pool = concurrent.futures.ProcessPoolExecutor(max_workers=20)
    axis = [10, 20, 30, 40, 50,]
    # yxis = []
    for i in axis:
        single_thread(i)
        async_get(i)
        multithrad(i)
        multiprocess(i)
        
# 运行结果
single_thread 请求次数10 所需用时: 50.13 s
async_get 请求次数10 所需用时: 5.02 s
multithrad 请求次数10 所需用时: 5.03 s
multiprocess 请求次数10 所需用时: 6.68 s
    
single_thread 请求次数20 所需用时: 100.32 s
async_get 请求次数20 所需用时: 5.06 s
multithrad 请求次数20 所需用时: 5.06 s
multiprocess 请求次数20 所需用时: 7.97 s
    
single_thread 请求次数30 所需用时: 150.6 s
async_get 请求次数30 所需用时: 5.04 s
multithrad 请求次数30 所需用时: 10.12 s
multiprocess 请求次数30 所需用时: 11.95 s
    
single_thread 请求次数40 所需用时: 200.57 s
async_get 请求次数40 所需用时: 5.07 s
multithrad 请求次数40 所需用时: 10.07 s
multiprocess 请求次数40 所需用时: 13.0 s
    
single_thread 请求次数50 所需用时: 250.74 s
async_get 请求次数50 所需用时: 5.09 s
multithrad 请求次数50 所需用时: 15.1 s
multiprocess 请求次数50 所需用时: 17.32 s
```

可以看到异步请求基本数都是5s之内获取,对比单线程多线程多进程效果还是不错的。

### 如何使用`asyncio`

基于`asyncio`的事件循环

```python
#!/usr/bin/env python3
# countasync.py

import asyncio

async def count():
    print("One")
    await asyncio.sleep(1)
    print("Two")

async def main():
    await asyncio.gather(count(), count(), count())

if __name__ == "__main__":
    import time
    s = time.perf_counter()
    asyncio.run(main())
    elapsed = time.perf_counter() - s
    print(f"{__file__} executed in {elapsed:0.2f} seconds.")
    
# 结果
One
One
One
Two
Two
Two
```

基于`for loop`的过程循环

```python
	#!/usr/bin/env python3
# countsync.py

import time

def count():
    print("One")
    time.sleep(1)
    print("Two")

def main():
    for _ in range(3):
        count()

if __name__ == "__main__":
    s = time.perf_counter()
    main()
    elapsed = time.perf_counter() - s
    print(f"{__file__} executed in {elapsed:0.2f} seconds.")

# 运行结果
One
Two
One
Two
One
Two
countsync.py executed in 3.01 seconds.
```

**未完待续**

### 参考

-   深入理解 Python 异步编程 https://gist.github.com/baymaxium/53af1bca2123dbd0cb82154455e7d48e
-   Python 异步编程入门 https://www.ruanyifeng.com/blog/2019/11/python-asyncio.html 
-   python高性能编程 https://python-parallel-programmning-cookbook.readthedocs.io/zh_CN/latest/chapter4/03_Event_loop_management_with_Asyncio.html
-   https://realpython.com/async-io-python/
-   python异步历史 https://snarky.ca/how-the-heck-does-async-await-work-in-python-3-5/

