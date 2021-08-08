---
title: "美妙的git"
subtitle: ""
date: 2021-08-08T12:14:41+08:00
lastmod: 2021-08-08T12:14:41+08:00
draft: true
author: "Createitv"
description: "Createitv"

page:
    theme: "classic"

upd: ""
authorComment: ""

tags: []
categories: ["git"]

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

## Git的故事

![img](https://linuxstory.org/wp-content/uploads/2015/04/git-training-philadelphia-pa.png)

十年前的这一周，[Linux](https://linuxstory.org/tag/linux/) 内核开发社区正面临严峻的挑战：他们不能继续使用 [BitKeeper](https://linuxstory.org/tag/bitkeeper/) 了（注：原因是当时[BitKeeper](https://linuxstory.org/tag/bitkeeper/) 著作权所有者决定收回授权，内核开发团队与其协商无果），而又没有其他的 SCM （Software Configuration Management）可满足他们的分布式系统的需求。[Linux](https://linuxstory.org/tag/linux/) 之父 [Linus Torvalds](https://linuxstory.org/tag/linus-torvalds/) 接受了这个挑战，决定开发一个新的版本控制系统。周末他消失了，新的一周，[Git](https://linuxstory.org/tag/git/) 问世了。今天，[Git](https://linuxstory.org/tag/git/) 已经成为上万个项目的版本控制系统，并且在程序员中引发了开源热潮。

git /gɪt/ 是一个开源的分布式版本控制系统

最初是 Linus Torvalds 为了帮助管理 Linux 内核开发而开发的一个开放源码的版本控制软件

## 有关版本控制

版本控制是一种记录一个或若干文件内容变化，以便将来查阅特定版本修订情况的系统。可以对任何类型的文件进行版本控制，便于不同的开发者协同工作

**集中式版本控制**

集中化的版本控制系统是为了让不同系统上的开发者协同工作。例如 **SVN**，它会有一个单一的集中管理的服务器，保存所有文件的修订版本，而协同工作的人通过客户端连接到这台服务器，拉取最新的文件或者是提交更新

在这个系统中，每个人可以看到项目中其他人的工作，管理员也能很好的掌握和分配每个开发者的权限。但由于版本库是集中在服务器上的，如果出现了中央服务器的单点故障，在这个时间内，谁都无法提交更新，而且整个项目的历史记录被保存在单一位置，就有丢失所有历史更新记录的风险

**分布式版本控制**

分布式的版本控制解决了集中化版本控制的一些问题，客户端并不只提取最新版本的文件快照，而是把代码仓库完整地镜像下来。这么一来，任何一处协同工作用的服务器发生故障，事后都可以用任何一个镜像出来的本地仓库恢复。因为每一次的克隆操作，实际上都是一次对代码仓库的完整备份

更进一步，许多这类系统都可以指定和若干不同的远端代码仓库进行交互。籍此，你就可以在同一个项目中，分别和不同工作小组的人相互协作。你可以根据需要设定不同的协作流程，比如层次模型式的工作流，而这在以前的集中式系统中是无法实现的

## Git 介绍

**直接记录快照，而非差异比较**

Git 保存的不是文件的变化或者差异，而是一系列不同时刻的文件快照

Git 把数据看作是对小型文件系统的一组快照。 每次提交更新，或在 Git 中保存项目状态时，它会对当时的全部文件制作一个快照并保存这个快照的索引。 为了高效，如果文件没有修改，Git 不再重新存储该文件，而是只保留一个链接指向之前存储的文件。Git 对待数据更像是一个快照流

**近乎所有操作都是本地执行**

在 Git 中的绝大多数操作都只需要访问本地文件和资源，因为你在本地磁盘上就有项目的完整历史，所以大部分操作看起来瞬间完成，

**Git 保证完整性**

Git 中所有数据在存储前都计算校验和，然后以校验和来引用

**Git 一般只添加数据**

执行的 Git 操作，几乎只往 Git 数据库中增加数据

## Git使用参考

## 1. 安装

```bash
brew install git
```

## 2. 配置

`--global`电脑全局配置，该命令只需要运行一次，因为之后无论你在该系统上做任何事情， Git 都会使用那些信息。你也可以在项目内配置项目的git，配置完后可以使用 `git config --list` 查看配置信息

```bash
$ git config --global user.name "zou"
$ git config --global user.email zouxq412@foxmail.com

```

## Git基本使用

### 1. Git 工作区、暂存区和版本库

在了解 Git 的基本操作之前，我们先来了解 Git 工作区、暂存区和版本库的概念

在 Git 中的文件有三种状态：*已提交(committed)：* 表示数据已经安全的保存在本地数据库中；*已修改(modified)：* 表示修改了文件，但还没保存到数据库中；*已暂存(staged)：* 表示对一个已修改文件的当前版本做了标记，使之包含在下次提交的快照中

由此也引入了 Git 项目的三个工作区域的概念：

- **工作区：** 就是项目文件所在的目录
- **暂存区：** stage 或 index。一般存放在 `.git/index` 文件中，所以我们把暂存区有时也叫作索引
- **版本库：** 工作区下隐藏目录 `.git`，这里记录着仓库的版本信息和历史记录

下面这个图展示了工作区、版本库中的暂存区和版本库之间的关系：



![img](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/10/31/16e1f6b9b4972fda~tplv-t2oaga2asx-watermark.awebp)

### 2.命令介绍

```bash
git init 								# 初始化git仓库，创建git配置文件
git add .  							# 添加所有文件到暂存区
git commit -m "<msg>"   # 提交文件更新
git commit -a "<msg>"   # 等于第上两步，直接跳过git add 步骤
git reset HEAD <file>... # 用来取消已暂存的内容
git status              # 参看git工作区状态
git rm <file> 					# 记录此次移除文件的操作
git rm -f <file>        # 如果把文件从暂存区域移除，但仍然希望保留在当前工作目录中，换句话说，仅是从跟踪清单中删除，使用 --cached 选项即可
git mv                  # 命令用于移动或重命名一个文件、目录、软连接
```

### 3. 上传github

```bash
git remote add origin <url> # 添加一个新的远程 Git 仓库
git push -u origin master   # 本地仓库或把已存在的仓库推到 github仓库上
git fetch 									# 命令会将数据拉取到你的本地仓库
git merge 									# 合并分支
git pull <remote> <branch>  # 通常会从最初克隆的服务器上抓取数据并自动尝试合并到当前所在的分支
git remote rename 					# 去修改一个远程仓库的简写名
git remote rm [name] 				# 可以去移除一个远程仓库
```

### 4.标签管理

```bash
git tag  									#查看所有的标签
git tag <name>  					#创建标签
git tag -v <name> -m <message>	# 创建附注标签
git push origin [tagname] #推送标签到远程仓库
git tag -d <tagname>  #删除本地仓库标签
git push <remote> :refs/tags/<tagname>  #更新远程仓库标签


```

### 5.分支管理

```bash
git branch 													#列出分支，当前分支前面会标一个*号
git branch <branchname> 						#创建分支
git checkout <branchname> 					#切换分支
git checkout -b <branchname> 				#创建并切换分支
git merge 													#合并分支
git branch -d <branchname> 					#删除分支

```



## 参考

- [Git 10 周年访谈：Linus Torvalds 讲述背后故事](https://linuxstory.org/10-years-of-git-an-interview-with-git-creator-linus-torvalds/)

