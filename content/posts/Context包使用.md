---
title: "Context包使用"
subtitle: ""
date: 2022-01-02T10:33:43+08:00
lastmod: 2022-01-02T10:33:43+08:00
draft: true
author: "Createitv"
description: "Createitv"

page:
    theme: "classic"

upd: ""
authorComment: ""

tags: []
categories: ["golang"]

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



## **协程如何退出**

### 1. 运行完毕自行退出

Context是控制并发的另一种方式。一个协程启动后，大部分情况需要等待里面的代码执行完毕，然后协程会自行退出。但是如果有一种情景，需要让协程提前退出怎么办呢?

```go
package main

import (
	"fmt"
	"sync"
	"time"
)

func main() {
	var wg sync.WaitGroup
	wg.Add(1)
	go func() {
		defer wg.Done()
		watchDog()
	}()
	wg.Wait()
}

func watchDog() {
	for {
		select {
		default:
			fmt.Println("正在监控房间动静")
		}
		time.Sleep(1 * time.Second)
	}
}
```

第一端持续监控的代码，程序无法自行终断。如果需要让监控狗停止监控、退出程序，一个办法是定义一个全局变量，其他地方可以通过修改这个变量发出停止监控狗的通知。然后在协程中先检查这个变量，如果发现被通知关闭就停止监控，退出当前协程。

### 2.变量监控

```go
package main

import (
	"fmt"
	"sync"
	"time"
)

func main() {
	var wg sync.WaitGroup
	wg.Add(1)
	stop := make(chan bool)
	go func() {
		defer wg.Done()
		watchDog(stop)
	}()
	time.Sleep(5 * time.Second)
	stop <- false
	wg.Wait()

}

func watchDog(stop chan bool) {
	for {
		select {
		case <- stop: //外部传递的结束信号
			fmt.Println("收到结束信号马上停止goroutine")
			return
		default:
			fmt.Println("正在监控房间动静")
		}
		time.Sleep(1 * time.Second)
	}
}
```

使用 select+channel 的方式改造的 watchDog 函数，实现了通过 channel 发送指令让监控狗停止，进而达到协程退出的目的

### 3.Context协程管理

```jsx
package main

import (
	"context"
	"fmt"
	"sync"
	"time"
)

func main() {
	var wg sync.WaitGroup
	wg.Add(1)
	// stop := make(chan bool)
	ctx, stop := context.WithCancel(context.Background())
	go func() {
		defer wg.Done()
		watchDog(ctx)
	}()
	time.Sleep(5 * time.Second)
	// stop <- false
	stop()
	wg.Wait()

}

func watchDog(ctx context.Context) {
	for {
		select {
		case <- ctx.Done():
			fmt.Println("收到结束信号马上停止goroutine")
			return
		default:
			fmt.Println("正在监控房间动静")
		}
		time.Sleep(1 * time.Second)
	}
}
```

- 使用 context.WithCancel(context.Background()) 函数生成一个可以取消的 Context，用于发送停止指令。这里的 context.Background() 用于生成一个空 Context，一般作为整个 Context 树的根节点。
- 原来的 stop <- true 停止指令，改为 context.WithCancel 函数返回的取消函数 stop()。

可以实现同样的功能看起来更优雅、而且功能更加全面

```jsx
package main

import (
	"context"
	"fmt"
	"sync"
	"time"
)

func main() {
	var wg sync.WaitGroup
	wg.Add(1)
	// stop := make(chan bool)
	ctx, cancel := context.WithTimeout(context.Background(), 5*time.Second)
	go func() {
		defer wg.Done()
		watchDog(ctx)
	}()
	time.Sleep(10 * time.Second)
	// stop <- false
	cancel()
	wg.Wait()

}

func watchDog(ctx context.Context) {
	for {
		select {
		case <- ctx.Done():
			fmt.Println("收到结束信号马上停止goroutine")
			return
		default:
			fmt.Println("正在监控房间动静")
		}
		time.Sleep(1 * time.Second)
	}
}
```

**context.WithTimeout设定几秒之后传递Cancel信号结束goroutine**

## 什么是Context

一个任务会有很多个协程协作完成，一次 HTTP 请求也会触发很多个协程的启动，而这些协程有可能会启动更多的子协程，并且无法预知有多少层协程、每一层有多少个协程。

如果因为某些原因导致任务终止了，HTTP 请求取消了，那么它们启动的协程怎么办？该如何取消呢？因为取消这些协程可以节约内存，提升性能，同时避免不可预料的 Bug。

Context 就是用来简化解决这些问题的，并且是并发安全的。Context 是一个接口，它具备手动、定时、超时发出取消信号、传值等功能，主要用于控制多个协程之间的协作，尤其是取消操作。一旦取消指令下达，那么被 Context 跟踪的这些协程都会收到取消信号，就可以做清理和退出操作。

Context 接口只有四个方法，下面进行详细介绍，在开发中你会经常使用它们，你可以结合下面的代码来看。

```go
type Context interface {

   Deadline() (deadline time.Time, ok bool)

   Done() <-chan struct{}

   Err() error

   Value(key interface{}) interface{}

}
```

1. Deadline 方法可以获取设置的截止时间，第一个返回值 deadline 是截止时间，到了这个时间点，Context 会自动发起取消请求，第二个返回值 ok 代表是否设置了截止时间。
2. Done 方法返回一个只读的 channel，类型为 struct{}。在协程中，如果该方法返回的 chan 可以读取，则意味着 Context 已经发起了取消信号。通过 Done 方法收到这个信号后，就可以做清理操作，然后退出协程，释放资源。
3. Err 方法返回取消的错误原因，即因为什么原因 Context 被取消。
4. Value 方法获取该 Context 上绑定的值，是一个键值对，所以要通过一个 key 才可以获取对应的值。

Context 接口的四个方法中最常用的就是 Done 方法，它返回一个只读的 channel，用于接收取消信号。当 Context 取消的时候，会关闭这个只读 channel，也就等于发出了取消信号。

### **Context 树**

我们不需要自己实现 Context 接口，Go 语言提供了函数可以帮助我们生成不同的 Context，通过这些函数可以生成一颗 Context 树，这样 Context 才可以关联起来，父 Context 发出取消信号的时候，子 Context 也会发出，这样就可以控制不同层级的协程退出。

从使用功能上分，有四种实现好的 Context。

1. **空 Context**：不可取消，没有截止时间，主要用于 Context 树的根节点。
2. **可取消的 Context**：用于发出取消信号，当取消的时候，它的子 Context 也会取消。
3. **可定时取消的 Context**：多了一个定时的功能。
4. **值 Context**：用于存储一个 key-value 键值对。

从下图 Context 的衍生树可以看到，最顶部的是空 Context，它作为整棵 Context 树的根节点，在 Go 语言中，可以通过 context.Background() 获取一个根节点 Context。

![http://learn.lianglianglee.com/专栏/22 讲通关 Go 语言-完/assets/CgqCHl_EyHOARbBqAAKzKmhclWo807.png](https://typora-1300715298.cos.ap-shanghai.myqcloud.com/uPic/CgqCHl_EyHOARbBqAAKzKmhclWo807.png)

（四种 Context 的衍生树）

有了根节点 Context 后，这颗 Context 树要怎么生成呢？需要使用 Go 语言提供的四个函数。

1. **WithCancel(parent Context)**：生成一个可取消的 Context。
2. **WithDeadline(parent Context, d time.Time)**：生成一个可定时取消的 Context，参数 d 为定时取消的具体时间。
3. **WithTimeout(parent Context, timeout time.Duration)**：生成一个可超时取消的 Context，参数 timeout 用于设置多久后取消
4. **WithValue(parent Context, key, val interface{})**：生成一个可携带 key-value 键值对的 Context。

### Context传值

```go
package main

import (
	"context"
	"fmt"
	"sync"
	"time"
)

func main() {
	var wg sync.WaitGroup
	wg.Add(1)
	ctx, cancel := context.WithTimeout(context.Background(), 5*time.Second)
	valCtx := context.WithValue(ctx, "robotID", "客厅监控number：0001")
	go func() {
		defer wg.Done()
		watchDog(valCtx)
	}()
	time.Sleep(10 * time.Second)
	cancel()
	wg.Wait()

}

func watchDog(ctx context.Context) {
	for {
		select {
		case <-ctx.Done():
			fmt.Println("收到结束信号马上停止goroutine")
			return
		default:
			fmt.Println("正在监控房间动静")
			robotID := ctx.Value("robotID")

			fmt.Println("监控机器人为：", robotID)

			time.Sleep(1 * time.Second)
		}
		time.Sleep(1 * time.Second)
	}
}
```

通过 context.WithValue 函数存储一个 robotID为 2 的键值对，就可以在 getUser 函数中通过 ctx.Value("robotID") 方法把对应的值取出来，达到传值的目的。

### **Context 使用原则**

Context 是一种非常好的工具，使用它可以很方便地控制取消多个协程。在 Go 语言标准库中也使用了它们，比如 net/http 中使用 Context 取消网络的请求。

要更好地使用 Context，有一些使用原则需要尽可能地遵守。

1. Context 不要放在结构体中，要以参数的方式传递。
2. Context 作为函数的参数时，要放在第一位，也就是第一个参数。
3. 要使用 context.Background 函数生成根节点的 Context，也就是最顶层的 Context。
4. Context 传值要传递必须的值，而且要尽可能地少，不要什么都传。
5. Context 多协程安全，可以在多个协程中放心使用。

以上原则是规范类的，Go 语言的编译器并不会做这些检查，要靠自己遵守。

## **总结**

Context 通过 With 系列函数生成 Context 树，把相关的 Context 关联起来，这样就可以统一进行控制。一声令下，关联的 Context 都会发出取消信号，使用这些 Context 的协程就可以收到取消信号，然后清理退出。你在定义函数的时候，如果想让外部给你的函数发取消信号，就可以为这个函数增加一个 Context 参数，让外部的调用者可以通过 Context 进行控制，比如下载一个文件超时退出的需求。
