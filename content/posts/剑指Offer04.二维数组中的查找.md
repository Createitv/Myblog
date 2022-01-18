---
title: "剑指Offer04.二维数组中的查找"
subtitle: ""
date: 2022-01-17T08:29:39+08:00
lastmod: 2022-01-17T08:29:39+08:00
draft: true
author: "Createitv"
description: "Createitv"

page:
    theme: "classic"

upd: ""
authorComment: ""

tags: ["剑指Offer"]
categories: ["数据结构与算法"]

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



## 问题描述

在一个 n * m 的二维数组中，每一行都按照从左到右递增的顺序排序，每一列都按照从上到下递增的顺序排序。请完成一个高效的函数，输入这样的一个二维数组和一个整数，判断数组中是否含有该整数。

示例:

现有矩阵 matrix 如下：

[ [1,   4,  7, 11, 15], [2,   5,  8, 12, 19], [3,   6,  9, 16, 22], [10, 13, 14, 17, 24], [18, 21, 23, 26, 30] ] 给定 target = 5，返回 true。

给定 target = 20，返回 false。

限制：

0 <= n <= 1000

0 <= m <= 1000

## 解决思路

1. 二维数组一遍遍历，然后二分搜索，复杂度$O(nlogn)$
2. 站在右上角看。这个矩阵其实就像是一个Binary Search Tree。从右上角开始比较，比它大就往下数一行，比它小就往左数一列。复杂度$O(m+n)$

![Picture1.png](https://typora-1300715298.cos.ap-shanghai.myqcloud.com/uPic/6584ea93812d27112043d203ea90e4b0950117d45e0452d0c630fcb247fbc4af-Picture1.png)

## 解决代码

### Javascript版本

```jsx
var findNumberIn2DArray = function (matrix, target) {
  for (let i of matrix) {
    console.log(i)
    if (BinarySearch(i, target) === -1) {
      continue
    } else {
      return true
    }
  }
  return false
}

function BinarySearch(arr, target) {
  if (arr.length < 1) return -1
  else if (arr.length === 1) {
    if (arr[0] === target) return true
    else return -1
  }
  // 低位下标
  let lowIndex = 0
  // 高位下标
  let highIndex = arr.length - 1

  while (lowIndex <= highIndex) {
    // 中间下标
    const midIndex = Math.floor((lowIndex + highIndex) / 2)
    if (target < arr[midIndex]) {
      highIndex = midIndex - 1
    } else if (target > arr[midIndex]) {
      lowIndex = midIndex + 1
    } else {
      // target === arr[midIndex]
      return midIndex
    }
  }
  return -1
}
const arr = [[-5]]
console.log(findNumberIn2DArray(arr, -2))

// 2. 站在右上角看。这个矩阵其实就像是一个Binary Search Tree。从右上角开始比较，比它大就往下数一行，比它小就往左数一列。
/**
 * @param {number[][]} matrix
 * @param {number} target
 * @return {boolean}
 */
var findNumberIn2DArray = function (matrix, target) {
  if (matrix.length === 0 || matrix[0].length < 1) return false
  let i = 0
  let j = matrix[0].length - 1
  while (i < matrix.length && j >= 0) {
    if (target === matrix[i][j]) {
      return true
    } else if (target < matrix[i][j]) {
      j--
    } else {
      i++
    }
  }
  return false
}
```

### python3版本

```python
class Solution:
    def findNumberIn2DArray(self, matrix: List[List[int]],
                            target: int) -> bool:
        i, j = len(matrix) - 1, 0
        while i >= 0 and j < len(matrix[0]):
            if matrix[i][j] > target: i -= 1
            elif matrix[i][j] < target: j += 1
            else: return True
        return False
```

### golang版本

```go
func findNumberIn2DArray(matrix [][]int, target int) bool {
	row := len(matrix)
	if row == 0 {
		return false
	}
	for i, j := 0, len(matrix[0])-1; i < row && j >= 0; {
		if matrix[i][j] == target {
			return true
		} else if matrix[i][j] > target {
			j--
		} else {
			i++
		}
	}
	return false
}
```

### 提交执行结果

![image-20220117083332557](https://typora-1300715298.cos.ap-shanghai.myqcloud.com/uPic/image-20220117083332557.png)

### 一点想法

好的数据结构配好的算法，如何能把这样一个二维数组抽象成一棵二叉搜索树这个过程还需要时间。

