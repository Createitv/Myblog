---
title: "Golang占位符"
subtitle: ""
date: 2021-12-13T13:02:51+08:00
lastmod: 2021-12-13T13:02:51+08:00
draft: true
author: "Createitv"
description: "Createitv"

page:
    theme: "classic"

upd: ""
authorComment: ""

tags: []
categories: ["Golang"]

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

## 概念

什么是占位符？你就从表面意思理解，就是占位置，只不过有很多不同种类的位置，而且这个位置不是谁都能坐，是有要求的，在程序层面用于[格式化数据](https://www.zhihu.com/search?q=格式化数据&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A2148418575})。

## 哪些函数支持

在 Go 语言中，标准包 `fmt` 有好多格式化的[工具函数](https://www.zhihu.com/search?q=工具函数&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A2148418575})，函数名末尾通常以 `f` 结尾，列举如下：

- `fmt.Printf` 格式化[字符串](https://www.zhihu.com/search?q=字符串&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A2148418575})并打印到终端（标准输出）。
- `fmt.Sprintf` 格式化字符串并返回。
- `fmt.Sscanf` 从字符串中解析占位符的对应位置数据。
- `fmt.Fscanf` 从 `io.Reader` 类型中读取数据并解析占位符的对应位置数据，用于读取文件、终端（标准输入）。
- `fmt.Fprintf` 将格式化的字符串数据输出到 `io.Writer` 类型中，用于输出到文件。
- `fmt.Errorf` 格式化方式创建一个错误类型消息。

```go
type Website struct {
    Name string
}

// 打印结构体时
var site = Website{Name:"studygolang"}
```



| 占位符 |                  说明                  | 举例                                    | 输出                              |
| :----: | :------------------------------------: | --------------------------------------- | --------------------------------- |
|   %v   |           相应值的默认格式。           | Printf("%v", site)，Printf("%+v", site) | {studygolang}，{Name:studygolang} |
|  %#v   |           相应值的Go语法表示           | Printf("#v", site)                      | main.Website{Name:"studygolang"}  |
|   %T   |        相应值的类型的Go语法表示        | Printf("%T", site)                      | main.Website                      |
|   %t   |          单词 true 或 false。          | Printf("%t", true)                      | true                              |
|   %d   |               十进制表示               | Printf("%d", 0x12)                      | 18                                |
|   %f   |           有小数点而无指数，           | 例如 123.456                            |                                   |
|   %s   |  输出字符串表示（string类型或[]byte)   | Printf("%s", []byte("Go语言学习园地"))  | Go语言学习园地                    |
|   %q   | 双引号围绕的字符串，由Go语法安全地转义 | Printf("%q", "Go语言学习园地")          | "Go语言学习园地"                  |
|   %p   |         十六进制表示，前缀 0x          |                                         |                                   |

作者：潇洒哥老苗
链接：https://www.zhihu.com/question/489996619/answer/2148418575
来源：知乎
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。



### 普通占位符

先创建一个数据，作为打印的对象。

```go
type Example struct {
    Content string
}

var data = Example{Content: "例子"}
```

### 1. %v、%+v、%#v

**%v**：获取数据的值，如果实现了`error` 接口，仅表示错误消息。

```go
fmt.Printf("%v", data) 
// 输出 
{例子}

fmt.Printf("%v", errors.New("我错了"))
// 输出
我错了
```

**%+v**：获取数据的值，如果结构体，会携带字段名。

```go
fmt.Printf("%+v", data)
// 输出
{Content:例子}
```

**%#v**：获取数据的值，如果是结构体，会携带结构体名和字段名。

```go
fmt.Printf("%#v", data)
// 输出
main.Example{Content:"例子"}
```

### 2. %T

获取数据类型。

```go
fmt.Printf("%T", data)
// 输出
main.Example
```

### 3. %%

字面上的一个百分号。

```go
fmt.Printf("%%")
// 输出
%
```

## 布尔占位符

### %t

true 或 false。

```go
fmt.Printf("%t", true)
// 输出
true
```

## 整数占位符

### 1. %b

[二进制](https://www.zhihu.com/search?q=二进制&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A2148418575})。

```go
fmt.Printf("%b", 4)
// 输出
100
```

### 2. %c

`Unicode` 码转字符。

```go
fmt.Printf("%c", 0x82d7)
// 输出
苗
```

### 3. %d、%5d、%-5d、%05d

十进制整数表示。

```go
fmt.Printf("%d,%d,%d", 10, 010, 0x10)
// 输出
10,8,16
```

- 三个数据： `10` 十进制，`010` [八进制](https://www.zhihu.com/search?q=八进制&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A2148418575})，`0x10` 十六进制

占位符还可以指定最小宽度，格式如下：

- `%5d`：最小宽度为 5，右对齐，左边留白。
- `%-5d`：左对齐，右边留白。
- `%05d`：数字位数不足 5 位时，左边补零。

例：

```go
fmt.Printf("|%5d|%-5d|%05d|", 1, 1, 1)
// 输出
|    1|1    |00001|
```

### 4. %o、%#o

八进制表示。

```go
fmt.Printf("%o,%o,%o", 10, 010, 0x10)
// 输出
12,10,20
```

在很多开发语言中，0 打头的数字都表示八进制。通过 `%#o` 输出带 0 开头。

```go
fmt.Printf("\n%#o\n", 10)
// 输出
012
```

### 5. %q

同 `%c` 类似，都是`Unicode` 码转字符，只是结果多了单引号。

```go
fmt.Printf("%q", 0x82d7)
// 输出
'苗'
```

汉字对应表：[http://www.chi2ko.com/tool/CJK.htm](https://link.zhihu.com/?target=http%3A//www.chi2ko.com/tool/CJK.htm)

### 6. %x、%#x

十六进制表示，字母形式为小写 a-f，`%#x` 输出带 0x 开头。

```go
fmt.Printf("%x, %#x", 13, 13)
// 输出
d, 0xd
```

### 7. %X、%#X

十六进制表示，字母形式为小写 A-F，`%#X` 输出带 0X 开头。

```go
fmt.Printf("%X, %#X", 13, 13)
// 输出
D, 0XD
```

### 8. %U、%#U

**%U**：转化为 Unicode 格式规范。

```go
fmt.Printf("%U", 0x82d7)
// 输出
U+82D7
```

**%#U**：转化为 Unicode 格式并带上对应的字符。

```go
fmt.Printf("%#U", 0x82d7)
// 输出
U+82D7 '苗'
```

## 浮点数与[复数](https://www.zhihu.com/search?q=复数&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A2148418575})

### 1. %b

浮点数转化为 2 的幂的科学计数法。

```go
fmt.Printf("%b", 0.1)
// 输出
7205759403792794p-56
```

### 2. %e、%E

10 的幂的科学计数法。

```go
fmt.Printf("%e", 10.2)
// 输出
1.020000e+01

fmt.Printf("%E", 10.2)
// 输出
1.020000E+01
```

区别：`%e` 与 `%E` 输出时的大小写之分。

### 3. %f、%.2f 等等

浮点数，`%.2f` 表示保留 2 位小数，`%f` 默认保留 6 位，`%f` 与 `%F` 等价。

>  保留的规则我现在还没有搞清楚，有时候符合[四舍五入](https://www.zhihu.com/search?q=四舍五入&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A2148418575})，有时候不符合，容我下来研究下，再告诉大家。 
>  

```go
fmt.Printf("%f", 10.2)
// 输出
10.200000

fmt.Printf("%.2f|%.2f", 10.232, 10.235)
// 输出
10.23|10.23
```

也可以加入最小宽度，如下：

- `%9.2f` 宽度最小为 9，包含小数位再内，精度为 2。
- `%9.f` 或 `%9f` 宽度最小为 9。

### 4. %g、%.3g

根据情况选择 `%e` 或 `%f` ，但末尾去除 0。

`%f` 情况如下：

```go
fmt.Printf("%g|%g", 10.20, 1.200000+3.400000i)
// 输出
10.2|(1.2+3.4i)
```

`%e` 情况如下：

```go
fmt.Printf("%g|%g", 2e2, 2E2)
// 输出
200|200
```

`%.3g` 表示的不是[小数](https://www.zhihu.com/search?q=小数&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A2148418575})保留 3 位，而是只保留 3 个数字。

```go
fmt.Printf("%.3g", 12.34)
// 输出
12.3
```

## 字符串与字节切片

### 1. %s

字符串或字节切片。

```go
fmt.Printf("%s|%s", "老苗", []byte("嘿嘿嘿"))
// 输出
老苗|嘿嘿嘿
```

### 2. %q

有 Go 语言安全转义，双引号包裹。

```go
fmt.Printf("%q", "老苗")
// 输出
"老苗"
```

### 3. %x、%X

十六进制，`%x` 小写字母 a - f，`%X` 大写字母 A - F。

```go
fmt.Printf("%x|%X", "苗", "苗")
// 输出
e88b97|E88B97
```

## 指针

### %p、%#p

地址，使用十六进制表示，`%p` 带 0x，`%#p` 不带。

```go
num := 2
s := []int{1, 2}
fmt.Printf("%p|%p", &num, s)

// 输出
0xc00000a1d0|0xc00000a1e0
```



### 参考：

- https://pkg.go.dev/fmt
- https://www.zhihu.com/question/489996619
- https://docs.kilvn.com/The-Golang-Standard-Library-by-Example/chapter01/01.3.html

