---
title: "Http协议"
subtitle: ""
date: 2022-02-05T18:28:08+08:00
lastmod: 2022-02-05T18:28:08+08:00
draft: true
author: "Createitv"
description: "Createitv"

page:
    theme: "classic"

upd: ""
authorComment: ""

tags: []
categories: ["http"]

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



# HTTP协议

## 1.HTTP协议基本认识

### HTTP历史

![image-20220205164448949](https://typora-1300715298.cos.ap-shanghai.myqcloud.com/uPic/image-20220205164448949.png)

### TCP/IP协议簇

TCP/IP协议簇是一个***\*四层协议系统\****，自顶向下分别是：应用层 传输层 网路层 数据链路层。每一层完成不同的功能，且通过若干协议来实现，上层协议使用下层协议提供的服务。

![img](https://typora-1300715298.cos.ap-shanghai.myqcloud.com/uPic/webp.webp)

### TCP三次握手

为了确定连接的可靠性，tcp协议采用了三次握手策略，确保发送端和客户端都在正常工作。

## 2.HTTP特点

### HTTP特点

#### 数据类型灵活

HTTP允许传输任意类型的数据对象，正在传输的内容由Content-Type加以标记。

#### 无状态连接

每次连接只处理一个连接，处理完客户请求即断开连接，每次的请求都是新的一次请求。

### HTTP请求报文

![image-20220205170817557](https://typora-1300715298.cos.ap-shanghai.myqcloud.com/uPic/image-20220205170817557.png)

HTTP的报文头大体可以分为四类，分别是**通用报文头**、**请求报文头**、**响应报文头**和实体报文头

![image-20220205171020809](https://typora-1300715298.cos.ap-shanghai.myqcloud.com/uPic/image-20220205171020809.png)

**Connection：keep-alve**当一个网页打开完成后，客户端和服务器之间用于传输HTTP数据的TCP连接不会关闭，如果客户端再次访问。这个服务器上的网页，会继续使用这一条已经建立的连接。

**Connection： close**代表个 Reques垸完成后，客户端和服务器之间用于传输HTP数据的TCP连接会关闭，当客户端再次发送Request，需要重新建立TCP连接

![image-20220205171130734](https://typora-1300715298.cos.ap-shanghai.myqcloud.com/uPic/image-20220205171130734.png)

![image-20220205171211542](https://typora-1300715298.cos.ap-shanghai.myqcloud.com/uPic/image-20220205171211542.png)

![image-20220205171236918](https://typora-1300715298.cos.ap-shanghai.myqcloud.com/uPic/image-20220205171236918.png)

**Content-Type**◆作用：说明了报文体内对象的媒体类型
text/hmtl：HTML格式
text/plain：纯文本格式
text/xml：XML格式
Image/gif：gi图片格式
Image/jpeg：jpg图片格式
Image/png：png图片格式

application/ Xhtml+xml： XHTML格式
application/xml：ⅩML数据格式
application/atom+xml： Atom XML聚合格式
application/json：JSoN数据格式
application/pdf pdf格式
application/msword：word文档格式
application/octet-stream：二进制流数据（如常见的文件下载）
application/WWW-form-urlencoded：表单提交

### HTTP响应报文

![image-20220205171658034](https://typora-1300715298.cos.ap-shanghai.myqcloud.com/uPic/image-20220205171658034.png)

### HTTP请求方法

**HTTP/1.1常用方法** ①GET ②POST ③PUT ④HEAD ⑤ DELETE ⑥OPTIONS ⑦ TRACE ⑧ CONNECT

**OPTIONS**用来査询针对请求∪R指定的资源支持的方法

**TRACE**回显服务器收到的请求，主要用于测试或诊断
**CONNECT**开启一个客户端与所请求资源之间的双向沟通的通道，它可以用来创建隧道

### HTTP请求状态码

常见的 HTTP 状态码：

|      | 类别                             | 原因短语                   |
| :--- | :------------------------------- | :------------------------- |
| 1XX  | Informational（信息性状态码）    | 接收的请求正在处理         |
| 2XX  | Success（成功状态码）            | 请求正常处理完毕           |
| 3XX  | Redirection（重定向）            | 需要进行附加操作以完成请求 |
| 4XX  | Client Error（客户端错误状态码） | 服务器无法处理请求         |
| 5XX  | Server Error（服务器错误状态码） | 服务器处理请求出错         |



### HTTP无状态解决方案：Cookies和Session

HTTP 协议是一种`无状态协议`，即每次服务端接收到客户端的请求时，都是一个全新的请求，服务器并不知道客户端的历史请求记录；Session 和 Cookie 的主要目的就是为了弥补 HTTP 的无状态特性。

**Cookie**的工作原理
（1）浏览器端第一次发送请求到服务器端
（2）服务器端创建Cookie，该Cookie中包含用户的信息，然后将该Cookie发送到浏览器端
（3）浏览器端再次访问服务器端时会携带服务器端创建的Cookie
（4）服务器端通过Cookie中携带的数据区分不同的用户

**Session**的工作原理
（1）浏览器端第一次发送请求到服务器端，服务器端创建一个Session，同时会创建一个特殊的Cookie（name为JSESSIONID的固定值，value为session对象的ID），然后将该Cookie发送至浏览器端
（2）浏览器端发送第N（N>1）次请求到服务器端,浏览器端访问服务器端时就会携带该name为JSESSIONID的Cookie对象
（3）服务器端根据name为JSESSIONID的Cookie的value(sessionId),去查询Session对象，从而区分不同用户。
name为JSESSIONID的Cookie不存在（关闭或更换浏览器），返回1中重新去创建Session与特殊的Cookie
name为JSESSIONID的Cookie存在，根据value中的SessionId去寻找session对象
value为SessionId不存在**（Session对象默认存活30分钟）**，返回1中重新去创建Session与特殊的Cookie
value为SessionId存在，返回session对象

**区别：**

-   cookie数据保存在客户端，session数据保存在服务端。

-   cookie不是很安全，别人可以分析存放在本地的COOKIE并进行COOKIE欺骗,如果主要考虑到安全应当使用session
-   session会在一定时间内保存在服务器上。当访问增多，会比较占用你服务器的性能，如果主要考虑到减轻服务器性能方面，应当使用COOKIE

## 3.通信协议结构和通信原理

### 字符集和编码

在HTTP中，与字符集和字符编码相关的信息头是Accept-Charset/Content-Type，另外主要区分Accept-Charset、Accept-Encoding、Accept-Language、Content-Type、Content-Encoding、Content-Language

### 基础身份认证

#### BASIC认证（基本认证）

![image-20220205173633603](https://typora-1300715298.cos.ap-shanghai.myqcloud.com/uPic/image-20220205173633603.png)

DIGEST认证虽然采用的是Base64编码方式，但是不是采用的加密处理，不需任何附加信息便可对Base64进行解码，达不到多数Web网站希望的安全性等级。

**DIGEST认证**

质询/响应的方式：值一方会先发送认证要求给另一方，接着使用从另一方那接收到的质询码计算生成响应码，最后将响应码返回给对方进行认证的一种方式

**SSL客户端认证**

SSL客户端认证是借由HTTPS的客户端证书完成认证的方式。凭借客户端证书认证服务器可确认访问是否来自已登录的客户端

高度的安全等级，费用较高。

**FormBase认证**

Web应用表单提交认证最常见，有许多的安全问题。

### 长连接与短连接

HTTP的长连接和短连接本质上是TCP长连接和短连接。

在HTTP/1.0中默认使用短连接。也就是说，客户端和服务器每进行一次HTTP操作，就建立一次连接，任务结束就中断连接。当客户端浏览器访问的某个HTML或其他类型的Web页中包含有其他的Web资源（如JavaScript文件、图像文件、CSS文件等），每遇到这样一个Web资源，浏览器就会重新建立一个HTTP会话。

而从HTTP/1.1起，默认使用长连接，用以保持连接特性。使用长连接的HTTP协议，会在响应头加入这行代码：

```makefile
Connection:keep-alive
```

在使用长连接的情况下，当一个网页打开完成后，客户端和服务器之间用于传输HTTP数据的TCP连接不会关闭，客户端再次访问这个服务器时，会继续使用这一条已经建立的连接。

### HTTP代理

Fiddler代理，Express代理。

### HTTP网关

网关可以作为某种翻译器使用，它抽象出了一种能够到达资源的方法。网关是资源和应用程序之间的粘合剂。网关扮演的是“协议转换器”的角色。

网关可以向数据库发送查询数据，发邮件，生成动态内容，像一个门一样，进入一个请求，出来一个响应。最常见的网关，应用程序服务器，会将目标服务器与网关结合在一个服务器中实现。

应用程序服务器是服务器端网关，与客户端通过HTTP进行通信，并与服务器端的应用程序相连。客户端是通过HTTP连接到应用程序服务器的。但应用程序服务器并没有回送文件，而是将请求通过一个网关应用编程接口(Application Programming Interface，API)发送给运行在服务器上的应用程序。

### HTTP缓存

http缓存指的是: 当客户端向服务器请求资源时，会先抵达浏览器缓存，如果浏览器有“要请求资源”的副本，就可以直接从浏览器缓存中提取而不是从原始服务器中提取这个资源。常见的http缓存只能缓存get请求响应的资源。

HTTP缓存机制是根据HTTP报文的缓存标识进行的。HTTP缓存分为强缓存和协商缓存。优先级最高的是强缓存，在命中强缓存失败的情况下，才会走协商缓存。

#### 强缓存

强缓存是利用http头中的Expires和Cache-Control两个字段来控制的。强缓存中，当请求再次发出时，浏览器会根据其中的Expires和Cache-Control判断目标资源是否“命中”强缓存，如果命中则直接从缓存中获取资源，不会再与服务端发生通信。命中强缓存的情况下，返回的HTTP状态码为200。

Cache-Control包含的值很多：

-   **public**：表明响应可以被任何对象（包括：发送请求的客户端、代理服务器等等）缓存。
-   **private**：表明响应只能被客户端缓存。
-   **no-cache**：跳过强缓存，直接进入协商缓存阶段。
-   **no-store**：表示当前请求资源禁用缓存
-   **max-age=**：设置缓存存储的最大周期，超过这个时间缓存被认为过期（单位秒）
-   **s-maxage=**：覆盖max-age或者Expires头。如果s-maxage未过期，则向代理服务器请求其缓存内容

#### 协商缓存

协商缓存，也称为对比缓存。协商缓存机制下，浏览器需要向服务器去询问缓存的相关信息，进而判断是重新发起请求、下载完整的响应，还是从本地获取缓存的资源。控制协商缓存的字段分别有：Last-Modified和Etag，其中Etag的优先级比Last-Modified高

### HTTP断点续传

也就是要从文件已经下载的地方开始继续下载。在以前版本的 HTTP 协议是不支持断点的，HTTP/1.1 开始就支持了。一般断点下载时才用到 Range 和 Content-Range 实体头。

**Range**

用于请求头中，指定第一个字节的位置和最后一个字节的位置，一般格式：

Range:(unit=first byte pos)-[last byte pos]

**Content-Range**

用于响应头，指定整个实体中的一部分的插入位置，他也指示了整个实体的长度。在服务器向客户返回一个部分响应，它必须描述响应覆盖的范围和整个实体长度。一般格式：

Content-Range: bytes (unit first byte pos) - [last byte pos]/[entity legth]



## 3.HTTPS

HTTPS（全称：Hypertext Transfer Protocol over Secure Socket Layer），是以安全为目标的HTTP通道，简单讲是HTTP的安全版。即HTTP下加入SSL层，HTTPS的安全基础是SSL，因此加密的详细内容就需要SSL



![img](https://typora-1300715298.cos.ap-shanghai.myqcloud.com/uPic/webp.webp)

-   服务器为每个客户端生成一个公钥，将公钥发送给客户端
-   客户端选择一个加密算法，然后用公钥加密以后发送给服务器
-   服务器收到这个公钥加密后的算法以后拿自己的私钥解密，然后就知道这个加密算法是哪个了。今后就一直用这个算法通信。

### CA证书

可以自己制作证书，然后把这个证书的公钥放在客户端（例如app的安装目录下），这样app只要使用自己的证书公钥即可 解密了，不需要使用系统的。

### HTTPS常见问题

-   费用 
-   连接耗时

## 4.HTTP改良协议

### WebSocket协议

WebSocket建立在HTTP基础上，发起方还是客户端，但是在建立连接后，客户端和服务器都可直接向对方发送报文。

为了实现WebSocket通信，在HTTP建立连接后，需要完成一次“握手”的步骤，需要用到HTTP的Upgrade首部字段，以达到握手的目的。

成功握手确定WebSocket连接后，通信时不再使用HTTP数据帧，采用WebSocket独立的数据帧。

![IMG_20210529_235354.jpg](https://typora-1300715298.cos.ap-shanghai.myqcloud.com/uPic/d3b0c59b23e343d5ab2af94335ae2b4ftplv-k3u1fbpfcp-watermark.awebp)

### HTTP2.0

HTTP2.0在2014-11实现标准化。目标是改善用户在使用Web中的速度体验。

## 5.HTTP题目

访问www.baidu.com之后发生了什么

![image-20220205163703373](https://typora-1300715298.cos.ap-shanghai.myqcloud.com/uPic/image-20220205163703373.png)

 (1) 浏览器获取输入的域名www.baidu.com 

  (2) 浏览器向DNS请求解析www.baidu.com的IP地址 

  (3) 域名系统DNS解析出百度服务器的IP地址 

  (4) 浏览器与该服务器建立TCP连接(默认端口号80) 

  (5) 浏览器发出HTTP请求，请求百度首页 

  (6) 服务器通过HTTP响应把首页文件发送给浏览器 

  (7) TCP连接释放 

  (8) 浏览器将首页文件进行解析，并将Web页显示给用户。
