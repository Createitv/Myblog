---
title: "剑指offer 03数组中重复的数字"
subtitle: ""
date: 2022-01-15T13:24:57+08:00
lastmod: 2022-01-15T13:24:57+08:00
draft: true
author: "Createitv"
description: "Createitv"

page:
    theme: "classic"

upd: ""
authorComment: ""

tags: ["剑指offer"]
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

找出数组中重复的数字。

在一个长度为 n 的数组 nums 里的所有数字都在 0～n-1 的范围内。数组中某些数字是重复的，但不知道有几个数字重复了，也不知道每个数字重复了几次。**请找出数组中任意一个重复的数字(不是第一个)**。

示例 1：

输入： [2, 3, 1, 0, 2, 5, 3] 输出：2 或 3

限制：

2 <= n <= 100000

## 解决思路

1. 遍历字典排序，每多一次值+1
2. 集合去重，记录数组的各个数字，当查找到重复数字则直接返回
3. 先对列表排序，排完以后重复元素必定相邻，故只需判断相邻元素是否相等即可

## 解决过程

### 坑一：

for in 循环只遍历属性不遍历值。`for ... in`是为遍历对象属性而构建的，不建议与数组一起使用，数组可以用`Array.prototype.forEach()`和`for ... of`

```jsx
for (const [key, value] in Object.entries(j)) 
```

尽管对于处理存储数据，数组更实用些，但是你在处理有`key-value`数据（比如属性用作“键”），需要检查其中的任何键是否为某值的情况时，还是推荐用`for ... in`。

`for ... in`代码举例

```jsx
var obj = {a:1, b:2, c:3};

for (var prop in obj) {
  console.log("obj." + prop + " = " + obj[prop]);
}

// Output:
// "obj.a = 1"
// "obj.b = 2"
// "obj.c = 3"
```

`for ... of` 代码举例

```jsx
const array1 = ['a', 'b', 'c'];

如果你不想修改语句块中的变量 , 也可以使用const代替let。
for (const element of array1) {
  console.log(element);
}

// expected output: "a"
// expected output: "b"
// expected output: "c"
var findRepeatNumber = function (nums) {
  let j = {}
  for (let i of nums) {
    if (!(i + '' in j)) {
      j[i] = 1
      continue
    }
    j[i] += 1
  }
  console.log(Object.entries(j));
  for (const [key, value] of Object.entries(j)) {
    if (value > 1) {
      return key
    }
  }
};

console.log(findRepeatNumber([5, 2, 23, 5, 3, 1, 0, 2, 5, 3]));
```

### 坑二

Object.entries(j)如果key是字符串遍历后的顺序会按照key大小排序。

```jsx
var findRepeatNumber = function (nums) {
  let j = {}
  for (let i of nums) {
    if (!(i + '' in j)) {
      j[i] = 1
      continue
    }
    j[i] += 1
  }
  console.log(Object.entries(j));
  for (const [key, value] of Object.entries(j)) {
    if (value > 1) {
      return key
    }
  }
};

console.log(findRepeatNumber([100, 12312, 5, 2, 23, 5, 3, 1, 0, 2, 5, 3]));
[
  [ '0', 1 ],
  [ '1', 1 ],
  [ '2', 2 ],
  [ '3', 2 ],
  [ '5', 3 ],
  [ '23', 1 ],
  [ '100', 1 ],
  [ '12312', 1 ]
]
```

如果key是字母不会排序

```jsx
const obj = { d: 5, b: 7, c: 9 };
for (const [key, value] of Object.entries(obj)) {
  console.log(`${key} ${value}`); // "d 5", "b 7", "c 9"
}
```

## 解决代码

### Javascript版本

```jsx
执行用104ms，在所有提交中击败了21.83%的用户
内存消耗：52.6MB，在所有提交中击败了4.99%的用户
// 哈希表版本一
存储所有数组中的元素，再循环判断字典的值是否多余一个
var findRepeatNumber = function (nums) {
  let j = {}
  for (let i of nums) {
    if (!(i + '' in j)) {
      j[i] = 1
      continue
    }
    j[i] += 1
  }
  for (let i of nums) {
    if (j[i] > 1) {
      return i
    }

  }
};

执行用时：88ms，在所有 JavaScript提交中击败了41.88%的用户
内存消耗：44.2MB，在所有 JavaScript提交中击败了6770%的用户
//哈希表版本二
不用存储所有数组中的元素，第一次判断字典中有没有此元素，无赋值true，有直接返回
var findRepeatNumber = function(nums) {
    let obj = {}
    let cur
    for(let i = 0; i < nums.length; i++){
        cur = nums[i]
        if(obj[cur] == true){
            return cur
        }else{
            obj[cur] = true
        }
    }
}

//集合版本
var findRepeatNumber = function(nums) {
    let map = new Map();
    for(let i of nums){
        if(map.has(i)) return i;
        map.set(i, 1);
    }
    return null;
};
```

### python3版本

```python
执行用时：44ms，在所有提交中击败了58.04%的用户
内存消耗：23.5MB，在所有提交中击败了53.09%的用户
class Solution:
    def findRepeatNumber(self, nums: [int]) -> int:
        dic = set()
        for num in nums:
            if num in dic: return num
            dic.add(num)
        return -1
```

同样的解体思路，python3运行时间更短，内存消耗更少

### golang版本

```go
执行用时：32ms，在所有Go提交中击败了88.77%的用户
内存消耗：8MB，在所有Go提交中击败了47.74%的用户
package main

import "fmt"

func findRepeatNumber(nums []int) int {
	m := make(map[int]bool)
	for _, i := range nums {
		if _, ok := m[i]; ok {
			return i
		} else {
			m[i] = true
		}
	}
	return -1
}

func main() {
	
// map测试代码
	m := map[string]int{
		"a": 1,
		"b": 2,
		"c": 3,
	}

	fmt.Println("Geting values")
	courseName, ok := m["d"] //ok为false
	fmt.Println(courseName, ok)

	courseName, ok = m["a"]
	fmt.Println(courseName, ok)
	if courseName, ok = m["d"]; ok {
		fmt.Println(courseName)
	} else {
		fmt.Println("key does not exist")
	}
// map测试代码

	fmt.Println(findRepeatNumber([]int{2, 3, 1, 0, 2, 5, 3}))

}
```

同样的解题办法，不得不吹golang一波。执行之时间最少，内存消耗也最少，代码看起来也易懂。

### 其他想法

这是第二遍刷剑指offer，第一遍的感觉和第二遍完全不同，第一遍只用了python3.API调用方便，只为囫囵吞枣的解决问题，基本上刷完了忘了。第二遍的刷题思路就完全不一样，第二遍着重看见运行效率。也就是leetcode判定的两个指标，运行时间和内存消耗。同样的问题，不同的解决问题的思路。效率千差万别，同样的问题，不同的编程语言来解决，虽然仅仅是API调用不同，但是运行效率却差很多。最后用高级语言的鼻祖C语言试试。仅仅对这一个特定问题基本上和Go没太大区别

```jsx
执行用时：32ms，在所有C提交中击败了72.84%的用户
内存消耗：11.9MB，在所有C提交中击败了7.82%的用户
int findRepeatNumber(int* nums, int numsSize) {
    int res = 0;
    int* hashTable = (int*)calloc(100000, sizeof(int));
    for (int i = 0; i < numsSize; i++) {
        if (hashTable[nums[i]] == 0) {
            hashTable[nums[i]]++;
        } else {
            res = nums[i];
            break;                
        }
    }
    return res;
}
```

知道为什么刷题很重要，刷题的思路，刷题的想法很重要，如果仅仅为了面试过了关，必定体会不到算法与程序设计的精髓。刷题的过程确实让我更加熟悉一些API，对于语言的合适常景的选择也有了一定的判断。
