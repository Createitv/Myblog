---
title: "Zap日志库"
subtitle: ""
date: 2022-01-23T22:18:32+08:00
lastmod: 2022-01-23T22:18:32+08:00
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

# Gin项目中的日志库管理

本文先介绍了Go语言原生的日志库的使用，然后详细介绍了非常流行的Uber开源的zap日志库，同时介绍了如何搭配Lumberjack实现日志的切割和归档

## 介绍

在许多Go语言项目中，我们需要一个好的日志记录器能够提供下面这些功能：

-   能够将事件记录到文件中，而不是应用程序控制台。
-   日志切割-能够根据文件大小、时间或间隔等来切割日志文件。
-   支持不同的日志级别。例如INFO，DEBUG，ERROR等。
-   能够打印基本信息，如调用文件/函数名和行号，日志时间等。

## 1.Go原生的日志管理

### 实现Go Logger

实现一个Go语言中的日志记录器非常简单——创建一个新的日志文件，然后设置它为日志的输出位置。

#### 设置Logger

我们可以像下面的代码一样设置日志记录器

```go
func SetupLogger() {
	logFileLocation, _ := os.OpenFile("/Users/q1mi/test.log", =os.O_CREATE|os.O_APPEND|os.O_RDWR, 0744)
	log.SetOutput(logFileLocation)
}
```

#### 使用Logger

让我们来写一些虚拟的代码来使用这个日志记录器。

在当前的示例中，我们将建立一个到URL的HTTP连接，并将状态代码/错误记录到日志文件中。

```go
func simpleHttpGet(url string) {
	resp, err := http.Get(url)
	if err != nil {
		log.Printf("Error fetching url %s : %s", url, err.Error())
	} else {
		log.Printf("Status Code for %s : %s", url, resp.Status)
		resp.Body.Close()
	}
}
```

#### Logger的运行

现在让我们执行上面的代码并查看日志记录器的运行情况。

```go
func main() {
	SetupLogger()
	simpleHttpGet("www.google.com")
	simpleHttpGet("http://www.google.com")
}
```

当我们执行上面的代码，我们能看到一个`test.log`文件被创建，下面的内容会被添加到这个日志文件中。

```bash
2019/05/24 01:14:13 Error fetching url www.google.com : Get www.google.com: unsupported protocol scheme ""
2019/05/24 01:14:14 Status Code for http://www.google.com : 200 OK
```

### Go Logger的优势和劣势

#### 优势

它最大的优点是使用非常简单。我们可以设置任何`io.Writer`作为日志记录输出并向其发送要写入的日志。

#### 劣势

-   仅限基本的日志级别
    -   只有一个`Print`选项。不支持`INFO`/`DEBUG`等多个级别。
-   对于错误日志，它有Fatal和Panic
    -   Fatal日志通过调用`os.Exit(1)`来结束程序
    -   Panic日志在写入日志消息之后抛出一个panic
    -   但是它缺少一个ERROR日志级别，这个级别可以在不抛出panic或退出程序的情况下记录错误
-   缺乏日志格式化的能力——例如记录调用者的函数名和行号，格式化日期和时间格式。等等。
-   不提供日志切割的能力。

## 2.Zap使用

[Zap](https://github.com/uber-go/zap)是非常快的、结构化的，分日志级别的Go日志库。

-   它同时提供了结构化日志记录和printf风格的日志记录
-   它非常的快

### 安装

运行下面的命令安装zap

```bash
go get -u go.uber.org/zap
```

### 配置Zap Logger

Zap提供了两种类型的日志记录器—`Sugared Logger`和`Logger`。

在性能很好但不是很关键的上下文中，使用`SugaredLogger`。它比其他结构化日志记录包快4-10倍，并且支持结构化和printf风格的日志记录。

在每一微秒和每一次内存分配都很重要的上下文中，使用`Logger`。它甚至比`SugaredLogger`更快，内存分配次数也更少，但它只支持强类型的结构化日志记录。

### 创建实例

通过调用`zap.NewProduction()`/`zap.NewDevelopment()`或者`zap.Example()`创建一个Logger。这三个方法的区别在于它将记录的信息不同，参数只能是`string`类型



```json
//代码
var log *zap.Logger
log = zap.NewExample()
log, _ := zap.NewDevelopment()
log, _ := zap.NewProduction()
log.Debug("This is a DEBUG message")
log.Info("This is an INFO message")

//Example 输出
{"level":"debug","msg":"This is a DEBUG message"}
{"level":"info","msg":"This is an INFO message"}

//Development 输出
2022-01-23T17:14:22.459+0800    DEBUG    development/main.go:7    This is a DEBUG message
2022-01-23T17:14:22.459+0800    INFO    development/main.go:8    This is an INFO message

//Production 输出
{"level":"info","ts":1540891173.3190675,"caller":"production/main.go:8","msg":"This is an INFO message"}
{"level":"info","ts":1540891173.3191047,"caller":"production/main.go:9","msg":"This i
```

三种创建方式对比：

- `Example`和`Production`使用的是[`json`](https://links.jianshu.com/go?to=https%3A%2F%2Fgodoc.org%2Fgo.uber.org%2Fzap%2Fzapcore%23NewJSONEncoder)格式输出，[`Development`](https://links.jianshu.com/go?to=https%3A%2F%2Fgodoc.org%2Fgo.uber.org%2Fzap%2Fzapcore%23NewConsoleEncoder)使用行的形式输出

- ```
  Development
  ```

  -   从警告级别向上打印到堆栈中来跟踪
  -   始终打印包/文件/行（方法）
  -   在行尾添加任何额外字段作为json字符串
  -   以大写形式打印级别名称
  -   以毫秒为单位打印ISO8601格式的时间戳

- ```
  Production
  ```

  -   调试级别消息不记录
  -   Error,Dpanic级别的记录，会在堆栈中跟踪文件，Warn不会
  -   始终将调用者添加到文件中
  -   以时间戳格式打印日期
  -   以小写形式打印级别名称

### 同时输出控制台和文件

如果需要同时输出控制台和文件，只需要改造一下`zapcore.NewCore`即可，示例：



```go
package main

import (
    "github.com/natefinch/lumberjack"
    "go.uber.org/zap"
    "go.uber.org/zap/zapcore"
    "os"
)

var log *zap.Logger

func main() {
    //获取编码器,NewJSONEncoder()输出json格式，NewConsoleEncoder()输出普通文本格式
    encoderConfig := zap.NewProductionEncoderConfig()
    encoderConfig.EncodeTime = zapcore.ISO8601TimeEncoder //指定时间格式
    encoderConfig.EncodeLevel = zapcore.CapitalLevelEncoder
    encoder := zapcore.NewConsoleEncoder(encoderConfig)

    //文件writeSyncer
    fileWriteSyncer := zapcore.AddSync(&lumberjack.Logger{
        Filename:   "./info.log", //日志文件存放目录
        MaxSize:    1,            //文件大小限制,单位MB
        MaxBackups: 5,            //最大保留日志文件数量
        MaxAge:     30,           //日志文件保留天数
        Compress:   false,        //是否压缩处理
    })
    fileCore := zapcore.NewCore(encoder, zapcore.NewMultiWriteSyncer(fileWriteSyncer,zapcore.AddSync(os.Stdout)), zapcore.DebugLevel) //第三个及之后的参数为写入文件的日志级别,ErrorLevel模式只记录error级别的日志

    log = zap.New(fileCore, zap.AddCaller()) //AddCaller()为显示文件名和行号


    log.Info("hello world")
    log.Error("hello world")
}
```

### 文件切割

日志文件会随时间越来越大，为了避免日志文件把硬盘空间占满，需要按条件对日志文件进行切割，zap包本身不提供文件切割的功能，但是可以用zap官方推荐的`lumberjack`包处理



```go
    //文件writeSyncer
    fileWriteSyncer := zapcore.AddSync(&lumberjack.Logger{
        Filename:   "./info.log", //日志文件存放目录，如果文件夹不存在会自动创建
        MaxSize:    1,            //文件大小限制,单位MB
        MaxBackups: 5,            //最大保留日志文件数量
        MaxAge:     30,           //日志文件保留天数
        Compress:   false,        //是否压缩处理
    })
```





### 按级别写入文件

为了管理人员的查询方便，一般我们需要将低于error级别的放到info.log，error及以上严重级别日志存放到error.log文件中，我们只需要改造一下`zapcore.NewCore`方法的第3个参数，然后将文件`WriteSyncer`拆成`info`和`error`两个即可，示例：



```go
package main

import (
    "github.com/natefinch/lumberjack"
    "go.uber.org/zap"
    "go.uber.org/zap/zapcore"
    "os"
)

var log *zap.Logger

func main() {
    var coreArr []zapcore.Core

    //获取编码器
    encoderConfig := zap.NewProductionEncoderConfig()               //NewJSONEncoder()输出json格式，NewConsoleEncoder()输出普通文本格式
    encoderConfig.EncodeTime = zapcore.ISO8601TimeEncoder           //指定时间格式
    encoderConfig.EncodeLevel = zapcore.CapitalColorLevelEncoder    //按级别显示不同颜色，不需要的话取值zapcore.CapitalLevelEncoder就可以了
    //encoderConfig.EncodeCaller = zapcore.FullCallerEncoder        //显示完整文件路径
    encoder := zapcore.NewConsoleEncoder(encoderConfig)

    //日志级别
    highPriority := zap.LevelEnablerFunc(func(lev zapcore.Level) bool{  //error级别
        return lev >= zap.ErrorLevel
    })
    lowPriority := zap.LevelEnablerFunc(func(lev zapcore.Level) bool {  //info和debug级别,debug级别是最低的
        return lev < zap.ErrorLevel && lev >= zap.DebugLevel
    })

    //info文件writeSyncer
    infoFileWriteSyncer := zapcore.AddSync(&lumberjack.Logger{
        Filename:   "./log/info.log",   //日志文件存放目录，如果文件夹不存在会自动创建
        MaxSize:    1,                  //文件大小限制,单位MB
        MaxBackups: 5,                  //最大保留日志文件数量
        MaxAge:     30,                 //日志文件保留天数
        Compress:   false,              //是否压缩处理
    })
    infoFileCore := zapcore.NewCore(encoder, zapcore.NewMultiWriteSyncer(infoFileWriteSyncer,zapcore.AddSync(os.Stdout)), lowPriority) //第三个及之后的参数为写入文件的日志级别,ErrorLevel模式只记录error级别的日志
    //error文件writeSyncer
    errorFileWriteSyncer := zapcore.AddSync(&lumberjack.Logger{
        Filename:   "./log/error.log",      //日志文件存放目录
        MaxSize:    1,                      //文件大小限制,单位MB
        MaxBackups: 5,                      //最大保留日志文件数量
        MaxAge:     30,                     //日志文件保留天数
        Compress:   false,                  //是否压缩处理
    })
    errorFileCore := zapcore.NewCore(encoder, zapcore.NewMultiWriteSyncer(errorFileWriteSyncer,zapcore.AddSync(os.Stdout)), highPriority) //第三个及之后的参数为写入文件的日志级别,ErrorLevel模式只记录error级别的日志

    coreArr = append(coreArr, infoFileCore)
    coreArr = append(coreArr, errorFileCore)
    log = zap.New(zapcore.NewTee(coreArr...), zap.AddCaller()) //zap.AddCaller()为显示文件名和行号，可省略

    log.Info("hello info")
    log.Debug("hello debug")
    log.Error("hello error")
}
```

这样修改之后，`info`和`debug`级别的日志就存放到`info.log`，`error`级别的日志单独放到`error.log`文件中了

### 自定义输出格式

```go
encoderConfig.EncodeTime =  zapcore.TimeEncoderOfLayout("2006-01-02 15:04:05")        // 自定义指定时间格式
```

## 3.Gin中Zap中间件封装

```go
func ZapLogger() gin.HandlerFunc {
	logger := config.InitLogger()
	return func(c *gin.Context) {
		start := time.Now()
		path := c.Request.URL.Path
		query := c.Request.URL.RawQuery
		c.Next()
		cost := time.Since(start)
		// *zap.Logger配置
		// logger.Info(path,
		// 	zap.Int("status", c.Writer.Status()),
		// 	zap.String("method", c.Request.Method),
		// 	zap.String("path", path),
		// 	zap.String("query", query),
		// 	zap.String("ip", c.ClientIP()),
		// 	zap.String("user-agent", c.Request.UserAgent()),
		// 	zap.String("errors", c.Errors.ByType(gin.ErrorTypePrivate).String()),
		// 	zap.Duration("cost", cost),
		// )
		logger.Infof("url %s statusCode: %d method: %s query: %s ip: %s user-agent: %s"+
			" errors: %s cost %s", path, c.Writer.Status(), c.Request.Method, query, c.ClientIP(), c.Request.UserAgent(),
			c.Errors.ByType(gin.ErrorTypePrivate).String(), cost)
	}
}
```

结果,此外还可以定制更多的请求消息

```bash
2022-01-23 22:11:02	INFO	middleware/logger.go:28	url /vps/health statusCode: 200 method: GET query:  ip: 127.0.0.1 user-agent: PostmanRuntime/7.28.0 errors:  cost 0s
2022-01-23 22:11:11	INFO	middleware/logger.go:28	url /vps/health statusCode: 200 method: GET query:  ip: 127.0.0.1 user-agent: PostmanRuntime/7.28.0 errors:  cost 0s
2022-01-23 22:11:11	INFO	middleware/logger.go:28	url /vps/ram statusCode: 200 method: GET query:  ip: 127.0.0.1 user-agent: PostmanRuntime/7.28.0 errors:  cost 0s
2022-01-23 22:11:11	INFO	middleware/logger.go:28	url /vps/cpu statusCode: 200 method: GET query:  ip: 127.0.0.1 user-agent: PostmanRuntime/7.28.0 errors:  cost 45.5196ms
2022-01-23 22:11:11	INFO	middleware/logger.go:28	url /vps/ram statusCode: 200 method: GET query:  ip: 127.0.0.1 user-agent: PostmanRuntime/7.28.0 errors:  cost 495.4µs
```

## 参考

https://www.liwenzhou.com/posts/Go/zap/

https://learnku.com/articles/62253

https://www.jianshu.com/p/910b626f67d9



