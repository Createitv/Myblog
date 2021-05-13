---
title: "javascript作用域"
date: 2021-05-13T16:54:42+08:00
draft: true
---

### let和var作用域

```js
// let声明块级作用域,仅在内部访问
// var 非函数体内声明全局作用域，外部亦可访问
if (true) {
    let y = 20;
    var z = 30;
    console.log(x + y + z);
		// -> 60
}
console.log(x + z);
// -> 40
```

```js
{
    // 使用在代码块外不可见的局部变量做一些工作
    let message = "Hello"; // 只在此代码块内可见
    alert(message); // Hello
}
alert(message); // Error: message is not defined
```

循环内

```js

for (let i = 0; i < 3; i++) {
  // 变量 i 仅在这个 for 循环的内部可见
  alert(i); // 0，然后是 1，然后是 2
}

alert(i); // Error, no such variable
```

每个作用域都可以“查看”包围它的作用域，异常情况是多个绑定具有相同的名称——在这种情况下，代码只能看到最里面的一个.

```js
const halve = function() {
    return n / 2;
};
let n = 10;
console.log(halve(10));
// -> 50
console.log(n)
// -> 10
```

### 嵌套作用域

内部函数可以访问外部变量

```js
function sayHiBye() {
    let firstName = "Yoo"
    let lastName = "Hoo";
    // 辅助嵌套函数使用如下
    function getFullName() {
        return firstName + " " + lastName;
    }
    console.log("Hello, " + getFullName());
    console.log("Bye, " + getFullName());
}
sayHiBye()
```

外部函数不能访问内部变量

```js
function sayHiBye() {
    let firstName = "Yoo"
    let lastName = "Hoo";
    // 辅助嵌套函数使用如下
    function getFullName() {
        var Name = "Yoohoo"
        return firstName + " " + lastName;
    }
    console.log("Hello, " + getFullName());
    console.log("Bye, " + getFullName());
    console.log(Name);
}
sayHiBye()
```

