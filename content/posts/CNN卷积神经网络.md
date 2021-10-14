---
title: "CNN卷积神经网络"
subtitle: ""
date: 2021-10-14T11:05:53+08:00
lastmod: 2021-10-14T11:05:53+08:00
draft: true
author: "Createitv"
description: "Createitv"

page:
    theme: "classic"

upd: ""
authorComment: ""

tags: ["CNN"]
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

本文以CNN算法的的领域之一图像识别为基础介绍CNN相关原理。

## 1. 人是如何识别物体

人眼识别物体涉及脑神经，视觉神经。属于我的知识盲区，大致是从光照射到物体反射到眼球，第一层视网神经到各中电信号生物信号器官部位传导们最后统一到视觉皮层处理形成影像。

## 2. 计算机是如何识别图片

图片的构成的基础单位像素，像素越大像素点越多。我们通常说的『图片分辨率』其实是指『像素数』（pixel count），通常表达为横向多少个像素x纵向多少个像素这样。像 480x800 这样的表述其实本来应该叫做尺寸（dimensions）的，但是因为数字图片并没有物理的长宽的概念，叫做尺寸反而可能会引起误解。数字图片的『宽』(width) 和『高』(height) 并非物理意义的长度单位，而是在两个维度上图片包含的像素个数。比如 480x800 这样的图片是由横向 480 个像素、纵向 800 个像素（合计 384 000 个像素点）构成的。![image-20211014121417356](https://typora-1300715298.cos.ap-shanghai.myqcloud.com/uPic/image-20211014121417356.png)

### 最简单的图像--二值图像

只有黑白。黑色代表前景色，白色代表背景色。Pytorch中MNIST手写数据集就是二值图像通道数为1。

![image-20211014121643414](https://typora-1300715298.cos.ap-shanghai.myqcloud.com/uPic/image-20211014121643414.png)

### 二值图像在计算机中的表示

![image-20211014121836063](https://typora-1300715298.cos.ap-shanghai.myqcloud.com/uPic/image-20211014121836063.png)

### 灰度图形在计算机中的表示

![image-20211014122224467](https://typora-1300715298.cos.ap-shanghai.myqcloud.com/uPic/image-20211014122224467.png)

### 彩色图像在计算机中的表示

颜色是我们对到达视网膜的各种频率的光的感觉。我们的视网膜有三种颜色感光视锥细胞，负责接收不同频率的光。这些感光器分类分别对应于红、绿和蓝三种颜色。人眼可以觉察的其他颜色都能由这三种颜色混合而成。这也是日常中最常见的颜色图。RGB三原色共同组成了任意色值像素。

以下为一些RGB像素值组成的颜色，也就是一个像素代表三个值（R， G， B）

![image-20211014122855563](https://typora-1300715298.cos.ap-shanghai.myqcloud.com/uPic/image-20211014122855563.png)

![image-20211014123026284](https://typora-1300715298.cos.ap-shanghai.myqcloud.com/uPic/image-20211014123026284.png)

彩色图像在pytorch也就是所谓的三通道Chanel就等于3。

### 利用numpy读取图片矩阵

图片样例：

![img](https://img2.baidu.com/it/u=1928696135,3556146331&fm=26&fmt=auto)

```python
from PIL import Image
import numpy as np
def read_img(img_name):
    img = Image.open(img_name)
    # 将读取的图像变为numpy矩阵
    np_img = np.array(img)  # (224, 224, 3)
    """
    将3维变为4维矩阵，可以用reshape去做变换，
    因为只有一张图片，可以加一个数组，然后让它封装在一个列表中，
    它就会把列表的这一维也加上去，列表的这一维就是1.
    """
    np_img = np.asarray([np_img], dtype=np.uint8)  # (1, 224, 224, 3)
    return np_img

im = read_img('1.jpg')
print(type(im))
# <class 'numpy.ndarray'>

print(im.dtype)
# uint8
print(im.shape)
# (1, 499, 500, 3)
print(im) # 得到图片在计算机中表示的三维数组

-----------代表一个三维数组，中间省略大部分-----------
[[[[245  82  37]
   [245  82  37]
   [245  82  37]
   ...
   [129 169  39]
   [168 201  99]
   [230 255 170]]
  [[245  82  37]
   [245  82  37]
   [245  82  37]
   ...
   [129 169  39]
   [168 201  99]
   [230 255 170]]
  [[245  82  37]
   [245  82  37]
   [245  82  37]
   ...
   [129 169  39]
   [168 201  99]
   [230 255 170]]
  ...
  [[  3 166 239]
   [  3 166 239]
   [  3 166 239]
   ...
   [231 189  55]
   [242 211  98]
   [255 238 135]]
  [[  3 166 239]
   [  3 166 239]
   [  3 166 239]
   ...
   [231 189  55]
   [242 211  98]
   [255 238 135]]
  [[  3 166 239]
   [  3 166 239]
   [  3 166 239]
   ...
   [231 189  55]
   [242 211  98]
   [255 238 135]]]]

# 输出第一排前5个像素点
print(im[0][0][0:5])
[[245  82  37]
 [245  82  37]
 [245  82  37]
 [245  82  37]
 [245  82  37]]
```



## 3. CNN卷积网络

CNN：Convolutional Neural Network（卷积神经网络）

![img](https://pic1.zhimg.com/80/v2-1614367fe2f6a8a0fe64c06b8e437e0b_1440w.jpg?source=1940ef5c)



### 发展史

卷积神经网络 (Convolutional Neural Network, CNN) 是一种目前广泛用于图像，自然语言处理等领域的深度神经网络模型。1998 年，Lecun 等人 [1](https://leovan.me/cn/2018/08/cnn/#fn:1) 提出了一种基于梯度的反向传播算法用于文档的识别。在这个神经网络中，卷积层 (Convolutional Layer) 扮演着至关重要的角色。

随着运算能力的不断增强，一些大型的 CNN 网络开始在图像领域中展现出巨大的优势，2012 年，Krizhevsky 等人 [2](https://leovan.me/cn/2018/08/cnn/#fn:2) 提出了 AlexNet 网络结构，并在 ImageNet 图像分类竞赛 [3](https://leovan.me/cn/2018/08/cnn/#fn:3) 中以超过之前 11% 的优势取得了冠军。随后不同的学者提出了一系列的网络结构并不断刷新 ImageNet 的成绩，其中比较经典的网络包括：VGG (Visual Geometry Group) [4](https://leovan.me/cn/2018/08/cnn/#fn:4)，GoogLeNet [5](https://leovan.me/cn/2018/08/cnn/#fn:5) 和 ResNet [6](https://leovan.me/cn/2018/08/cnn/#fn:6)。

CNN 在图像分类问题上取得了不凡的成绩，同时一些学者也尝试将其应用在图像的其他领域，例如：物体检测 [7](https://leovan.me/cn/2018/08/cnn/#fn:7)[8](https://leovan.me/cn/2018/08/cnn/#fn:8)[9](https://leovan.me/cn/2018/08/cnn/#fn:9)，语义分割 [10](https://leovan.me/cn/2018/08/cnn/#fn:10)，图像摘要 [11](https://leovan.me/cn/2018/08/cnn/#fn:11)，行为识别 [12](https://leovan.me/cn/2018/08/cnn/#fn:12) 等。除此之外，在非图像领域 CNN 也取得了一定的成绩 [13](https://leovan.me/cn/2018/08/cnn/#fn:13)。

### 模型原理

下图为 Lecun 等人提出的 LeNet-5 的网络架构：

![LeNet-5](https://leovan.me/images/cn/2018-08-25-cnn/lenet-5.png)

下面我们针对 CNN 网络中的不同类型的网络层逐一进行介绍。

### 输入层

LeNet-5 解决的手写数字分类问题的输入为一张 32x32 像素的灰度图像 (Gray Scale)。日常生活中计算机常用的图像的表示方式为 RGB，即将一张图片分为红色通道 (Red Channel)，绿色通道 (Green Channel) 和蓝色通道 (Blue Channel)，其中每个通道的每个像素点的数值范围为 [0,255]。灰度图像表示该图片仅包含一个通道，也就是不具备彩色信息，每个像素点的数值范围同 RGB 图像的取值范围相同。

因此，一张图片在计算机的眼里就是一个如下图所示的数字矩阵 (示例图片来自于 MNIST 数据集：

![Digit-Pixels](https://leovan.me/images/cn/2018-08-25-cnn/digit-pixels.png)

在将图像输入到 CNN 网络之前，通常我们会对其进行预处理，因为每个像素点的最大取值为 255，因此将每个像素点的值除以 255 则可以将其归一化到 [0,1] 的范围。

### 卷积层

![image-20211014153731931](https://typora-1300715298.cos.ap-shanghai.myqcloud.com/uPic/image-20211014153731931.png)

第一次卷积计算之后的结果

![image-20211014153924363](https://typora-1300715298.cos.ap-shanghai.myqcloud.com/uPic/image-20211014153924363.png)

在了解卷积层之前，让我们先来了解一下什么是卷积？

下图形象的刻画了利用一个 3x3 大小的卷积核的整个卷积计算过程：

![Conv-Sobel](https://leovan.me/images/cn/2018-08-25-cnn/conv-sobel.gif)

一些预设的卷积核对于图片可以起到不同的滤波器效果，例如下面 4 个卷积核分别会对图像产生不同的效果：不改变，边缘检测，锐化和高斯模糊。

![image-20211014152509181](https://typora-1300715298.cos.ap-shanghai.myqcloud.com/uPic/image-20211014152509181.png)

对 lena 图片应用这 4 个卷积核，变换后的效果如下 (从左到右，从上到下)：

![Lena-Filters](https://leovan.me/images/cn/2018-08-25-cnn/lena-filters.png)

在上面整个计算卷积的动图中，我们不难发现，利用 3x3 大小 (我们一般将这个参数称之为 `kernel_size`，即**卷积核的大小**，其可以为一个整数表示长宽大小相同，也可以为两个不同的整数) 的卷积核对 5x5 大小的原始矩阵进行卷积操作后，结果矩阵并没有保持原来的大小，而是变为了 (5-(3-1))x(5-(3-1)) (即 3x3) 大小的矩阵。这就需要引入 CNN 网络中卷积层的两个常用参数 `padding` 和 `strides`。

`padding` 是指是否对图像的外侧进行**补零操作**，其取值一般为 `VALID` 和 `SAME` 两种。`VALID` 表示**不进行补零**操作，对于输入形状为 (x,y) 的矩阵，利用形状为 (m,n) 的卷积核进行卷积，得到的结果矩阵的形状则为 (x−m+1,y−n+1)。

![Conv2d-Zero-Padding](https://leovan.me/images/cn/2018-08-25-cnn/conv-zero-padding.png)

`strides` 是指进行卷积操作时，每次卷积核移动的步长。示例中，卷积核在横轴和纵轴方向上的移动步长均为 1，除此之外用于也可以指定不同的步长。移动的步长同样会对卷积后的结果的形状产生影响。

除此之外，还有另一个重要的参数 `filters`，其表示在一个卷积层中使用的**卷积核的个数**。在一个卷积层中，一个卷积核可以学习并提取图像的一种特征，但往往图片中包含多种不同的特征信息，因此我们需要多个不同的卷积核提取不同的特征。下图 [15](https://leovan.me/cn/2018/08/cnn/#fn:15) 是一个利用 4 个不同的卷积核对一张图像进行卷积操作的示意图：

![Conv2d-Kernels](https://leovan.me/images/cn/2018-08-25-cnn/conv2d-kernels.png)

上面我们都是以一个灰度图像 (仅包含 1 个通道) 为示例进行的讨论，那么对于一个 RGB 图像 (包含 3 个通道)，相应的，卷积核也是一个 3 维的形状，如所示：

![Conv3d-Kernels](https://leovan.me/images/cn/2018-08-25-cnn/conv3d-kernels.png)



### 池化层

**池化层** 是一个利用 **池化函数 (pooling function)** 对网络输出进行进一步调整的网络层。池化函数使用某一位置的相邻输出的总体统计特征来代替网络在该位置的输出。常用的池化函数包括最大池化 (max pooling) 函数 (即给出邻域内的最大值) 和平均池化 (average pooling) 函数 (即给出邻域内的平均值) 等。但无论选择何种池化函数，当对输入做出少量平移时，池化对输入的表示都近似 **不变 (invariant)**。**局部平移不变性** 是一个很重要的性质，尤其是当我们关心某个特征是否出现而不关心它出现的位置时。

![image-20211014154418398](https://typora-1300715298.cos.ap-shanghai.myqcloud.com/uPic/image-20211014154418398.png)

池化层同卷积层类似，具有三个比较重要的参数：`pool_size`，`strides` 和 `padding`，分别表示池化窗口的大小，步长以及是否对图像的外侧进行补零操作。下图 是一个 `pool_size=3`，`strides=3`，`padding='valid'` 的最大池化过程示例：

![Max-Pooling](https://leovan.me/images/cn/2018-08-25-cnn/max-pooling.gif)

池化层同时也能够提高网络的计算效率，例如上图中在横轴和纵轴的步长均为 3，经过池化后，下一层网络节点的个数降低至前一层的 13×3=19。

### 激活函数(修正线性单元)

![image-20211014154859516](https://typora-1300715298.cos.ap-shanghai.myqcloud.com/uPic/image-20211014154859516.png)

### 全连接层

![image-20211014155812562](https://typora-1300715298.cos.ap-shanghai.myqcloud.com/uPic/image-20211014155812562.png)

全链接层 (Fully-connected or Dense Layer) 的目的就是将我们最后一个池化层的输出连接到最终的输出节点上。例如，最后一个池化层的输出大小为 [5×5×16]，也就是有 5×5×16=400 个节点，对于手写数字识别的问题，我们的输出为 0 至 9 共 10 个数字，采用 one-hot 编码的话，输出层共 10 个节点。例如在 LeNet 中有 2 个全连接层，每层的节点数分别为 120 和 84，在实际应用中，通常全连接层的节点数会逐层递减。需要注意的是，在进行编码的时候，第一个全连接层并不是直接与最后一个池化层相连，而是先对池化层进行 flatten 操作，使其变成一个一维向量后再与全连接层相连。

### 输出层

输出层根据具体问题的不同会略有不同，例如对于手写数字识别问题，采用 one-hot 编码的话，输出层则包含 10 个节点。对于回归或二分类问题，输出层则仅包含 1 个节点。当然对于二分类问题，我们也可以像多分类问题一样将其利用 one-hot 进行编码，例如 [1,0] 表示类型 0，[0,1] 表示类型 1。

### 卷积算力

一长800x600的彩色图片共1440000个像素点，好三个3x3的卷机核取计算大概需要1300万次乘法和1200次加法。

### 通用步骤

卷积 ->> 非线性激活 ->> 池化 == 降维

![image-20211014155134790](https://typora-1300715298.cos.ap-shanghai.myqcloud.com/uPic/image-20211014155134790.png)

多层神经网络

重复卷积、非线性激活、池化，运算量也不断增大

![image-20211014155245645](https://typora-1300715298.cos.ap-shanghai.myqcloud.com/uPic/image-20211014155245645.png)

### 超参数

训练之前定义的参数：卷积核尺寸，卷积核数目。池化步长，全连接数量。

## 4 MNIST数据集PytorchCNN训练

```python
# mnist 训练集
# 1.导入训练包
import numpy as np
import torch
import torch.nn as nn
import torch.nn.functional as F
import torch.optim as optim
from torchvision import datasets, transforms
from torch.utils.data import DataLoader
import cv2

# 2. 定义超参数
BATCH_SIZE = 16  # 每次批处理的数据
DEVICE = torch.device("cuda" if torch.cuda.is_available() else 'cpu')  # 是否使用GPU
EPOCHS = 10  # 训练轮次

# 3 构建pipleline，对图形做处理
pipleline = transforms.Compose([
    transforms.ToTensor(),  # 图片转化维张量
    transforms.Normalize((0.1307,), (0.3081,))  # 正则化，过拟合时候降低复制度
])

# 4 下载加载数据
# 下载数据集
train_set = datasets.MNIST("./data", train=True, download=True, transform=pipleline)
test_set = datasets.MNIST("./data", train=False, download=False, transform=pipleline)

# 加载数据集
train_loader = DataLoader(train_set, batch_size=BATCH_SIZE, shuffle=True)
test_loader = DataLoader(test_set, batch_size=BATCH_SIZE, shuffle=True)


# 5 构建神经网络
class Network(nn.Module):
    def __init__(self):
        super().__init__()
        self.conv1 = nn.Conv2d(1, 10, 5)  # 1图片通道，10：输出通道， 5 卷积核大小
        self.conv2 = nn.Conv2d(10, 20, 3)  # 10输入通道，20：输出通道， 3 卷积核大小
        self.fce1 = nn.Linear(20 * 10 * 10, 500)  # 20 * 10 * 10 输入通道， 500：输出通道
        self.fce2 = nn.Linear(500, 10)  # 500 输入通道， 10：输出通道

    def forward(self, x):
        """前向传播"""
        input_size = x.size(0)
        x = self.conv1(x)  # 输入：batch*1*28*28 输出batch*10*24*24 24等于图片像素-卷积核大小+1
        # 非线性激活
        x = F.relu(x)  # shape不变 输出 batch*10*24*24
        # 池化
        x = F.max_pool2d(x, 2, 2)  # 2步长，2池化核心大小 输入batch*10*24*24 输出batch*10*12*12
        x = self.conv2(x)  # 输入batch*10*12*12  输出batch*20*10*10  10等于图片像素-卷积核大小+1
        # 非线性激活
        x = F.relu(x)
        # 拉伸
        x = x.view(input_size, -1)  # -1 自动计算维度 20*10*10 = 2000
        # 全连接
        x = self.fce1(x)  # 输入：batch*2000  输出batch*500
        x = F.relu(x)
        x = self.fce2(x)  # 输入：batch*500 输出batch*10
        # 损失函数
        output = F.log_softmax(x, dim=1)  # 计算分类后， 每个数字的概率值, 得到损失函数
        return output


# 6 定义优化器
model = Network().to(DEVICE)
optimizer = optim.Adam(model.parameters())  # Adam 优化器


# 7 定义训练方法
def train_model(model, device, train_loader, optimizer, epoch):
    # 模型训练
    model.train()
    for batch_index, (data, target) in enumerate(train_loader):
        # 部署到devices
        data, target = data.to(device), target.to(device)
        # 初始化梯度
        optimizer.zero_grad()
        # 训练后的结果
        output = model(data)
        # 交叉墒， 计算损失
        loss = F.cross_entropy(output, target)  # 真是值和目标值
        # 找到概率值最大的下标
        pred = output.argmax(dim=1)
        # 反向传播
        loss.backward()
        # 参数优化
        optimizer.step()
        if batch_index % 3000 == 0:
            print("Train Epoch : {} \t Loss : {:.6f}loss".format(epoch, loss.item()))

# 8 定义测试方法
def test_model(model, device, test_loader):
    # 模型验证
    model.eval()
    # 正确率
    correct = 0.0
    # 测试损失
    test_loss = 0.0
    with torch.no_grad():  # 不会计算梯度和反向传播
        for data, target in test_loader:
            # 部署到DEVICE
            data, target = data.to(device), target.to(device)
            # 测试数据
            output = model(data)
            # 计算测试损失
            test_loss += F.cross_entropy(output, target).item()
            # 找到概率值最大的下标
            pred = output.argmax(dim=1)
            # 累计正确的值
            correct += pred.eq(target.view_as(pred)).sum().item()
        # 平均损失值
        test_loss /= len(test_loader.dataset)
        print(
            "Test Average loss : {:.4f} Accuracy : {:.3f}".format(test_loss, 100.0 * correct / len(test_loader.dataset)))


# 9 定义调用方法
for epoch in range(1, EPOCHS + 1):
    train_model(model, DEVICE, train_loader, optimizer, epoch)
    test_model(model, DEVICE,test_loader)
```



## 参考

- [人脸识别啥原理？人工智能（二）卷积神经网络](https://www.youtube.com/watch?v=AFlIM0jSI9I)
- [计算机视觉——图像的表示](https://www.zhihu.com/zvideo/1402038708572524544?playTime=641.3)
- [卷积神经网络 (Convolutional Neural Network, CNN)](https://leovan.me/cn/2018/08/cnn/)
- [How Convolutional Neural Networks work](https://www.youtube.com/watch?v=FmpDIaiMIeA)

