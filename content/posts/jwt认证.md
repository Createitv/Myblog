---
title: "Jwt认证"
subtitle: ""
date: 2021-12-20T14:51:18+08:00
lastmod: 2021-12-20T14:51:18+08:00
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



## JWT是什么？

JWT (JSON Web Token)是一个开放标准（[RFC 7519](https://tools.ietf.org/html/rfc7519)），指基于JSON的、用于在网络上声明某种主张的令牌（token），以保证各方之间安全的传输信息。

JWT通过将用户信息加密到token中，服务端不需要保存任何用户信息。服务端只需要通过保存的密钥来验证token正确性，如果正确即通过验证。

## JWT的组成

JWS实际上就是一个字符串，由三部分组成，头部(Header)、载荷(Payload)、签名(Signature)，并以`.`进行拼接。其中头部和载荷都是以JSON格式存放数据，只是进行了编码。

![jwt](https://typora-1300715298.cos.ap-shanghai.myqcloud.com/uPic/20211220144654.png)

### 1. 头部(Header)

每个JWT都会带有头部信息，这里主要声明使用的算法。声明算法的字段名为`alg`，同时还有一个`typ`的字段，默认`JWT`即可。以下示例中算法为HS256。

```go
{  "alg": "HS256",  "typ": "JWT" }
```

因为JWT是字符串，所以我们还需要对以上内容进行Base64编码，编码后字符串如下：

```go
`eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9 `
```



### 2. 载荷(Payload)

载荷即消息体，这里会存放实际的内容，也就是Token的数据声明(Claim)。这一段有一些是标准字段，当然也可以根据自己需要添加自己需要的字段。标准字段如下：

-   `iss`: Token签发者。格式是区分大小写的字符串或者uri，用于唯一标识签发token的一方。
-   `sub`: Token的主体，即它的所有人。格式是区分大小写的字符串或者uri。
-   `aud`: 接收Token的一方。格式为区分大小写的字符串或uri，或者这两种的数组。
-   `exp`: Token的过期时间，格式为时间戳。
-   `nbf`: 指定Token在nbf时间之前不能使用，即token开始生效的时间，格式为时间戳。
-   `iat`: Token的签发时间，格式为时间戳。
-   `jti`: 指此Token的唯一标识符字符串。主要用于实现唯一性保证，防止重放。

下面是一个示例：

```go
{  "sub": "1234567890",  "name": "John Doe",  "iat": 1516239022 }
```



同样进行Base64编码后，字符串如下：

```go
`eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ `
```



### 3. 签名(Signature)

签名是对头部和载荷内容进行签名，一旦前面两部分数据被篡改，只要服务器加密用的密钥没有泄露，得到的签名肯定和之前的签名不一致。

签名的过程：

1.  对header的json数据进行Base64URL编码，得到一个字符串str1
2.  对payload的json数据进行Base64URL编码，得到一个字符串str2
3.  使用`.`对以上两个字符串进行拼接，得到字符串str3
4.  使用header中声明的算法，以及服务端的密钥，对拼接字符串进行加密，生成签名

如果用伪代码表示就是(以HS256为例)：

```go
HMACSHA256(    base64UrlEncode(header) + "." +    base64UrlEncode(payload),    secret )
```



将三组字符串，以`.`相连，就得到了一个完整的token，例如：

```go
`eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.OFHM3R8PSyHDT_vuzRF5fYkYWdhExM_9pE81kG05qAk `
```



## 如何使用JWT？

在之前的传统的方法时在服务端存储一个session，并给客户端返回一个cookie。而如果是使用jwt来做身份鉴定的话，当用户登录成功，会给用户一个token，前端只需要在本地保存该token即可(通常使用localStorage，也可以使用cookie)。

当用户需要访问一个受保护的资源时，需要再Header中使用Bearer模式的Authorization头。其内容看起来是下面这样：

```go
`Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.OFHM3R8PSyHDT_vuzRF5fYkYWdhExM_9pE81kG05qAk `
```

## JWT的优缺点

优点：

-   json具有通用性，所以可以跨语言。
-   组成简单，字节占用小，便于传输
-   服务端无需保存会话信息，很容易进行水平扩展
-   一处生成，多处使用，可以在分布式系统中，解决单点登录问题
-   可防护CSRF攻击

缺点：

-   payload部分仅仅是进行简单编码，所以只能用于存储逻辑必需的非敏感信息
-   需要保护好加密密钥，一旦泄露后果不堪设想
-   为避免token被劫持，最好使用https协议
-   针对已经办法的令牌，无法作废，不容易应对数据过期的问题。

## Golang实现

```go
/ 指定加密密钥
var jwtSecret=[]byte(setting.JwtSecret)

//Claim是一些实体（通常指的用户）的状态和额外的元数据
type Claims struct{
	Username string `json:"username"`
	Password string `json:"password"`
	jwt.StandardClaims
}

// 根据用户的用户名和密码产生token
func GenerateToken(username ,password string)(string,error){
	//设置token有效时间
	nowTime:=time.Now()
	expireTime:=nowTime.Add(3*time.Hour)

	claims:=Claims{
		Username:       username,
		Password:       password,
		StandardClaims: jwt.StandardClaims{
            // 过期时间
			ExpiresAt:expireTime.Unix(),
			// 指定token发行人
			Issuer:"gin-blog",
		},
	}

	tokenClaims:=jwt.NewWithClaims(jwt.SigningMethodHS256,claims)
	//该方法内部生成签名字符串，再用于获取完整、已签名的token
	token,err:=tokenClaims.SignedString(jwtSecret)
	return token,err
}

// 根据传入的token值获取到Claims对象信息，（进而获取其中的用户名和密码）
func ParseToken(token string)(*Claims,error){

	//用于解析鉴权的声明，方法内部主要是具体的解码和校验的过程，最终返回*Token
	tokenClaims, err := jwt.ParseWithClaims(token, &Claims{}, func(token *jwt.Token) (interface{}, error) {
		return jwtSecret, nil
	})

	if tokenClaims!=nil{
		// 从tokenClaims中获取到Claims对象，并使用断言，将该对象转换为我们自己定义的Claims
		// 要传入指针，项目中结构体都是用指针传递，节省空间。
		if claims,ok:=tokenClaims.Claims.(*Claims);ok&&tokenClaims.Valid{
			return claims,nil
		}
	}
	return nil,err

}

```

### 参考

- https://www.ruanyifeng.com/blog/2018/07/json_web_token-tutorial.html
