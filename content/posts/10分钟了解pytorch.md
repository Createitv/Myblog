---
title: "10分钟了解pytorch"
subtitle: ""
date: 2021-10-13T17:53:01+08:00
lastmod: 2021-10-13T17:53:01+08:00
draft: true
author: "Createitv"
description: "Createitv"

page:
    theme: "classic"

upd: ""
authorComment: ""

tags: ["pytorch"]
categories: ["深度学习"]

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



# 什么是 Pytorch ？

1. 可以把 Pytorch 当做 Numpy 的替代方案使用，做科学计算，而且更强大。
2. 做 AI 任务的模型建模、训练、部署等。这方面唯一能与其比肩的是 Google 家的 Tensorflow。

本文是对 Pytorch 非常简短的一个介绍，目录如下：

1. Tensor 的创建与操作，cuda 加速计算。
2. Pytorch 的自动微分功能。

# Tensor 创建与操作

## Tensor 的创建

1. 创建一个未初始化的、形状为 (5,3) 的空 tensor。

```
x = torch.empty(5, 3)
print(x)
 
```

 

```
tensor([[1.0102e-38, 9.0919e-39, 1.0102e-38],
        [8.9082e-39, 8.4489e-39, 9.6429e-39],
        [8.4490e-39, 9.6429e-39, 9.2755e-39],
        [1.0286e-38, 9.0919e-39, 8.9082e-39],
        [9.2755e-39, 8.4490e-39, 1.0194e-38]])
 
```





1. 创建一个随机初始化，形状为 (5,3) 的 tensor。



```
x = torch.rand(5, 3)
print(x)
 
```

```
tensor([[0.4133, 0.0885, 0.0503],
        [0.6771, 0.5543, 0.8236],
        [0.3047, 0.1217, 0.4441],
        [0.6269, 0.6820, 0.4217],
        [0.5631, 0.8517, 0.8708]])
 
```

1. 创建一个0填充的、形状为 (5,3) 的 tensor。

```
x = torch.zeros(5, 3, dtype=torch.long)
print(x)
 
```

```
tensor([[0, 0, 0],
        [0, 0, 0],
        [0, 0, 0],
        [0, 0, 0],
        [0, 0, 0]])
 
```

直接从列表创建一个 tensor：

```
x = torch.tensor([5.5, 3])
print(x)
 
```

```
tensor([5.5000, 3.0000])
```

在现有 tensor 的基础上新建一个全1 tensor，默认情况下新的 tensor 会继承已有 tensor 的属性，比如形状、数据类型等，当然也可以手动指定。：

```
x = x.new_ones(5, 3, dtype=torch.double)
print(x)
```

```
tensor([[1., 1., 1.],
        [1., 1., 1.],
        [1., 1., 1.],
        [1., 1., 1.],
        [1., 1., 1.]], dtype=torch.float64)
```



生成一个与现有 tensor 一样形状的随机 tensor，且数据类型覆盖为 `torch.float`：

```
x = torch.randn_like(x, dtype=torch.float)
print(x)
 
```

 

```
tensor([[ 0.9629,  0.0349,  0.5597],
        [-2.1172,  1.1874, -0.1596],
        [ 0.6841, -0.6172, -0.4732],
        [ 0.0468, -0.3634,  1.1014],
        [ 0.6064,  0.1740,  0.2344]])
 
```



1. 查看 tensor 的形状

 ：

```
print(x.size())
 
```

 

```
torch.Size([5, 3])
 
```

`tensor.size`方法返回的是一个tuple对象，可以执行tuple的各种操作。

## tensor 的基本操作

详细内容可参考[官方文档](https://link.juejin.cn/?target=https%3A%2F%2Fpytorch.org%2Fdocs%2Fstable%2Ftorch.html%23indexing-slicing-joining-mutating-ops)

### 1. 创建

- torch.rand* 类随机生成 tensor 方法。

```
torch.rand()
torch.rand_like()
torch.randn()
torch.randn_like()
torch.randint()
torch.randint_like()
torch.randperm()
torch.empty()
 
```

- 从其他数据源创建

```
torch.tensor()
torch.from_numpy()
torch.full()
torch.range()
torch.linspace()
torch.eye()
 
```



### 2. 索引、切片、合并等转换

```
# 按指定维度拼接 tensor
torch.cat() 

# 按指定维度将一个 tensor 分割为几个小块
torch.chunk()

# 按照深度方向（第三维）将多个 tensor 拼接，若tensor不足三维，则先转为3维 tensor 再行拼接。
torch.dstack()

# 将多个 tensor 按第1维进行拼接
torch.hstack()

# 按指定维度进行索引查询
torch.index_select()

# 根据一个 BoolTensor 进行 mask 查询
torch.masked_select()

# 调换 tensor 维度
torch.movedim()

# 返回 tensor 中非0值的索引
torch.nonzero()

# 改变 tensor 形状
torch.reshape()

# 按照指定维度及指定大小将 tensor 分割为几块
torch.split()

# 去除所有 tensor 中大小为1的维度，也可以指定哪个维度。
torch.squeeze()

# 按照指定维度拼接多个 tensor
torch.stack()

# tensor 转置
torch.t()

# 根据给定的索引从已有 tensor 中抽取出一个新的 tensor。
torch.take()

# 将 tensor 的指定两个维度进行互换
torch.transpose()

# 在指定地方增加 tensor 维度
torch.unsqueeze()

# 类似 dstack，只是是在第二个维度进行拼接
torch.vstack()

# 对 tensor 中每个元素根据条件判断如何返回
torch.where()
 
```

### 3. 随机采样机器参数设置

```
# 设置非确定性随机数的随机种子
torch.seed

# 设置生成随机数的随机种子
torch.manual_seed

# 查看初始化的随机种子，为 `long` 类型
torch.initial_seed

# 查看随机数生成器状态
torch.get_rng_state

# 设置随机数生成器状态
torch.set_rng_state
 
```

### 4. 序列化与反序列化

```
# 对 Pytorch 中的对象进行序列化保存和读取
torch.save()
torch.load()
 
```

### 5. 并行计算

```
# 获取和设置 CPU 并行操作时的线程数
torch.get_num_threads
torch.set_num_threads

# 获取和设置 CPU 上的互操作并行的线程数
torch.get_num_interop_threads
torch.set_num_interop_threads
 
```

### 6. 梯度控制

有多重方法可以控制 tensor 是否计算梯度

 ：

```
x = torch.zeros(1, requires_grad=True)
with torch.no_grad():
    y = x * 2
print(y.requires_grad)


is_train = False
with torch.set_grad_enabled(is_train):
    y = x * 2
print(y.requires_grad)


torch.set_grad_enabled(True)
y = x * 2
print(y.requires_grad)
 
```

 

```
False
False
True
 
```

### 7. 数学操作

pytorch 支持大部分常见的数学操作，这里不详细列举，详见[官方文档](https://link.juejin.cn/?target=https%3A%2F%2Fpytorch.org%2Fdocs%2Fstable%2Ftorch.html%23indexing-slicing-joining-mutating-ops)

```
# Pointwise 操作
torch.abs
torch.clip
torch.cos
torch.sin
torch.div
torch.exp
torch.pow
torch.log
torch.sigmoid

# Reduction 操作
torch.argmax
torch.argmin
torch.max
torch.dist
torch.mean
torch.norm
torch.count_nonzero

# 比较操作
torch.allclose
torch.argsort
torch.eq
torch.equal
torch.ge
torch.gt
torch.isinf
torch.isfinite
torch.isnan
torch.isreal
torch.isneginf
torch.sort
torch.topk

# 光谱操作及其他操作
......
 
```





# Pytorch 的自动微分功能

Pytorch 具备自动微分功能。

1. requires_grad

这个参数表示这个 tensor是否需要计算梯度。

```
x = torch.ones(2, 2, requires_grad=True)
print(x)
 
```

 

```
tensor([[1., 1.],
        [1., 1.]], requires_grad=True)
 
```



1. grad_fn

指当前这个 tensor 是通过哪个函数得来的，在链式求导时会按照此函数进行计算。一般来讲除了用户自己创建的 tensor 外，如果是 pytorch 内置函数所生成的 tensor 都会有 grad_fn。

 ：

```
y = x + 2
print(y)
print(y.grad_fn)

z = y * y * 3
out = z.mean()
print(z, out)
 
```

 

```
tensor([[3., 3.],
        [3., 3.]], grad_fn=<AddBackward0>)
<AddBackward0 object at 0x000001CEE32753C8>
tensor([[27., 27.],
        [27., 27.]], grad_fn=<MulBackward0>) tensor(27., grad_fn=<MeanBackward0>)
 
```



1. backward & grad

backward 方法用于计算梯度，通过链式求导法则计算好的梯度就存在 tensor 的 grad 属性中。

 ：

```
out.backward()
print(x.grad)
 
```

 

```
tensor([[4.5000, 4.5000],
        [4.5000, 4.5000]])
 
```



1. with torch.no_grad

通过这种方法，可以避免在计算中的梯度计算

 ：

```
print(x.requires_grad)
print((x ** 2).requires_grad)
with torch.no_grad():
    print((x ** 2).requires_grad)
 
```

 

```
True
True
False
 
```



1. detach

通过 detach 方法避免梯度计算

 ：

```
print(x.requires_grad)
y = x.detach()
print(y.requires_grad)
print(x.eq(y).all())
 
```

 

```
True
False
tensor(True)
 
```



1. requires_grad_

设置 `requires_grad_` 属性可直接更改 tensor 梯度计算配置。

 ：

```
a = torch.randn(2, 2)
a = ((a * 3) / (a - 1))
print(a.requires_grad)
a.requires_grad_(True)
print(a.requires_grad)
b = (a * a).sum()
print(b.grad_fn)
 
```

 

```
False
True
<SumBackward0 object at 0x000001CEE32753C8>
 
```

