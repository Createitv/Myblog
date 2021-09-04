---
title: "Environs库"
subtitle: ""
date: 2021-09-02T21:31:55+08:00
lastmod: 2021-09-02T21:31:55+08:00
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

## 系统环境变量配置

### 设置 Windows PowerShell 环境变量

```powershell
$env:Path +=";C:\Program Files\GnuWin32\bin"
$env:string="a string"
```

### 查看 Windows PowerShell 环境变量

```powershell
$env:NUMBER_OF_PROCESSORS. # 查看系统CPU数量
```

### 设置 Windows CMD 环境变量

```cmd
set string="string"
```

### 查看 Windows CMD 环境变量

```cmd
set
```

### 设置 MacOS,Linux 环境变量

```bash
export PATH="/usr/local/sbin:$PATH"
export NODE_ENV=development
```

### 查看 MacOS,Linux 环境变量

```bash
echo $PATH
echo
```

## OS 内置模块

```python

# Python program to explain os.environ object

# importing os module
import os
import pprint

# Get the list of user's
# environment variables
env_var = os.environ

# Print the list of user's
# environment variables
print("User's Environment variable:")
pprint.pprint(dict(env_var), width = 1)
```

### 添加环境变量

```python

# Python program to explain os.environ object

# importing os module
import os

# Add a new environment variable
os.environ['GeeksForGeeks'] = 'www.geeksforgeeks.org'

# Get the value of
# Added environment variable
print("GeeksForGeeks:", os.environ['GeeksForGeeks'])

# Print the value of
# 'MY_HOME' environment variable
print("MY_HOME:", os.environ['MY_HOME']

# If the key does not exists
# it will produce an error
```

## environs 库特点

-   类型检查
-   直接读取本地`.env` 配置文件
-   自定义解析，闭 os.environs 更易用

## **安装**

> ```
> pip install environs
> ```

## 基本使用官方用例

> ```python
> # export GITHUB_USER=sloria
> # export API_KEY=123abc
> # export SHIP_DATE='1984-06-25'
> # export ENABLE_LOGIN=true
> # export GITHUB_REPOS=webargs,konch,ped
> # export COORDINATES=23.3,50.0
>
> from environs import Env
>
> env = Env()
> # reading an environment variable
> gh_user = env('GITHUB_USER')  # => 'sloria'
> secret = env('SECRET')  # => raises error if not set
>
> # cast
> ingapi_key = env.str('API_KEY')  # => '123abc'
> date = env.date('SHIP_DATE')  # => datetime.date(1984, 6, 25)
>
> # providing a default value
> enable_login = env.bool('ENABLE_LOGIN', False)  # => True
> enable_feature_x = env.bool('ENABLE_FEATURE_X', False)  # => False
>
> # parsing lists
> gh_repos = env.list('GITHUB_REPOS')  # => ['webargs', 'konch', 'ped']
> coords = env.list('COORDINATES', subcast=float)  # => [23.3, 50.0]
> ```

### 支持类型

总的来说，environs 支持的转化类型有这么多：

-   `env.str`
-   `env.bool`
-   `env.int`
-   `env.float`
-   `env.decimal`
-   `env.list` (accepts optional `subcast` keyword argument)
-   `env.dict` (accepts optional `subcast` keyword argument)
-   `env.json`
-   `env.datetime`
-   `env.date`
-   `env.timedelta` (assumes value is an integer in seconds)
-   `env.url`
-   `env.uuid`
-   `env.log_level`
-   `env.path` (casts to a [`pathlib.Path`](https://docs.python.org/3/library/pathlib.html))

### 读取本地`.env`文件

```python
# .env file
DEBUG=true
PORT=4567

from environs import Env

env = Env()
# Read .env into os.environ
env.read_env()

env.bool("DEBUG")  # => True
env.int("PORT")  # => 4567

```

### 前缀处理

environs 还支持前缀处理，一般来说我们定义一些环境变量，如数据库的连接，可能有 host、port、password 等，但在定义环境变量的时候往往会加上对应的前缀，如 MYSQL_HOST、MYSQL_PORT、MYSQL_PASSWORD 等，但在解析时，我们可以根据前缀进行分组处理，见下面的示例：

```python
# export MYAPP_HOST=localhost
# export MYAPP_PORT=3000

with env.prefixed("MYAPP_"):
    host = env("HOST", "localhost")  # => 'lolcathost'
    port = env.int("PORT", 5000)  # => 3000

# nested prefixes are also supported:

# export MYAPP_DB_HOST=localhost
# export MYAPP_DB_PORT=10101

with env.prefixed("MYAPP_"):
    with env.prefixed("DB_"):
        db_host = env("HOST", "lolcathost")
        db_port = env.int("PORT", 10101)
```

### 参考

-   https://cuiqingcai.com/8947.html
-   https://www.geeksforgeeks.org/python-os-environ-object/
