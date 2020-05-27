---
title: JavaScript从头来1-基本概念
comments: true
mathjax: true
categories:
  - 基础知识
tags:
  - js基础
  - 追本溯源
  - 笔记
date: 2020-03-27 06:47:24
---

> 经常在开发中，发现自己知识盲区。其实还是基础不牢固。所以一直想，抽时间从新走下，查漏补缺。

## javascript 简介

### 特点

- 解释型语言
- 类似于 c 和 java 的语法结构
- 动态语言
- 基于原型的面向对象
- 字面量的表现能力
- 函数式编程

### JavaScript 组成

- 浏览器客户端 :核心（ECMAScript）+ 文档对象模型（DOM）+ 浏览器对象模型（BOM）
- 服务端：核心（ECMAScript）+ 内置宿主对象

> ECMAScript 与浏览器没有依赖关系（本身不包含输入、输出定义）。ECMA-262 定义的是语言基础，可以在此基础上构建完善的脚本语言。
> 具体包含【语法、类型、语句、关键字、保留字、操作符、对象】

## 表达式和语句的构成

JavaScript 的源代码本质上是一个语句的集合。语句是由**语句**和**表达式**所构成的。表达式则由表达式和 运算符所构成。即使在一条语句中包含其他语句，只要对这条被包含的语句继续进行分解， 最终都会到达仅包含**保留字、表达式与符号**的状态。

### 关键字和保留字

就有特殊用途的--关键字，如：

    void break  return do case else new var ...

预留的不能用作特殊字符的保留字，如：

    boolean float native init short ...

### 标识符

标识符指变量、函数、属性的名字、或者函数的参数。

组合规则：

1. 第一个字符必须是字母、下划线（\_）、或者美元( \$ )
2. 其他字母可以是字母、下划线、美元或者数字
3. 驼峰写法： 第一个字母小写 剩下的每个单词首字母大写

- 必须是除保留字以外的单词。
- 必须是除 true、false、null 以外的单词。
- 必须是以 Unicode 的(非空)字符开始，之后接有 Unicode 字符或是数字的单词。
- 单词的长度并无限制。
- 习惯上，以下划线(\_)开始的标识符会被作为“内部标识符”来使用

### 字面量

字面量(literal)指的是，在代码中写下这些值之后，将会在运行时直接使用这些值的字面含义。

```js
// 字符串字面量"bar"的例子
var foo = 'bar'
// 数值字面量0的例子
var val0 = 0
```

| 名称       | 具体示例                 |
| ---------- | ------------------------ |
| 数值       | 100                      |
| 字符串值   | "foobar"                 |
| 布尔值     | true                     |
| null       | 值 null                  |
| Object     | { x:1, y:2 }             |
| 数列       | [3, 1, 2]                |
| 函数       | function() { return 0; } |
| 正则表达式 | /foo/                    |

### 区分大小写

变量、函数名、操作符都区分大小写

### 严格模式

头部添加`' use strict '`，表示在严格模式下的执行。

> 严格执行模式下，执行结果会有很大不同。支持浏览器：IE10+、Firefox4+、Safari5.1+、Opera12+、Chrome

---

### 语句

1. 每条语句，分号结尾（;）【 现代项目架构中，不加分号是编译后，统一由脚手架添加 】。
2. 复合语句，使用花括号 （{}）进行代码块分割，明确意图，降低出错情况。

### 变量的基础

变量的作用是给某一个值或是对象标注名称。ECMAscript 变量是非常松散的（可以保存任何**_类型_**的数据）。每个变量仅仅只是一个用于存值的占位符。

```js
// 操作符（关键字） + 变量名

var clock // 变量值是： undefined

var count = 1 // 变量值是： 1 【 等于： var count = undefined； count = 1; 】先声明 后负值d

function test() {
  num = 1
  // 有意 忽略 var 关键字，的确可以在局部作用域创建一个全局变量。
  // 但是不推荐这样使用会导致， 造成不必要的混乱
  var isShow = true // 局部变量
  console.log(count) // 1   count 是个全局变量
}

test()
console.log(isShow) //  错误 : isShow is not defined  因为 局部变量 无法访问
console.log(clockName) // undefined：  变量提升【只会提升变了声明 ，不回提升负值】
var clockName = 'littleClock'

// 批量
var message = 'hi',
  found = false,
  age = 29
```

#### 关于 变量、常量、字面量 的区别

> 变量、常量是引用，变量值可变，常量值固定。字面量是值

## 函数基础

> 函数本身也是一种对象。
>
> 正如变量存在的意义是为了调用没有名称的对象，函数名存在的意义是为了调用没有名称的函数。因此，**变量名和函数名实质上是相同的**。

```
  // 匿名函数的语法
  function (参数, 参数, ......) {
    函数体
  }
  function 函数名 (参数, 参数, ......) {
    函数体
  }
  var 函数名 = function(){
    函数体
  }
```

## 对象的基础

### 对象特点

- 属性值可以由函数指定
- 具备一种称为原型链的构造

#### 对象字面量表达式与对象的使用

```js
{
  key: value
}
```

> key 属性名可以是**标识符、字符串值或是数值**。value 属性值则可以是**任意的值或对象**。

#### 属性访问

- `.` 方式用于确定属性的**赋值**或**取值**：`obj.key=123`
- `[]`方式用于不确定值的属性[字符串类型]的**赋值**或**取值**：
  ```js
  var name = 'key'
  obj[name] = 123
  obj['id'] = 123
  ```

#### new 表达式

new 表达式的作用是生成一个对象。new 之后所写的是函数名,成的对象，其属性能够被 读取。

```js
var obj = new Object()
typeof obj // "object"
```

> JavaScript 中没 有类的概念，所以，根据 JavaScript 的语法规则，new 之后所写的是函数名。 就会把该函数作为构造函数来进行调用。

## 数组的基础

> 数组是一种用于表达有顺序关系的值的集合的语言结构。在 JavaScript 中，数组并非是一种内建类 型。相对地，JavaScript 支持 Array 类，所以数组能够以 Array 类的实例的形式实现。

```js
var arr = [1, 100, 7]
arr[1] = 100 // 读取索引值为1的元素
var n = 1
console.log(arr[n]) // 与a[1]含义相同 200
console.log(arr[n + 1]) // 与a[2]含义相同
```

### 数组内部元素可以是多种类型

```js
var arr = [1, '23', true, null, undefined]
arr.push(function () {
  return 123
})
// [1, "23", true, null, undefined,function(){return 123}]
```

#### 数组内部元素可以是多维

```js
var arr = [1, 2, 3]
arr.push([4, 5, 6])
console.log(arr) // [1, 2, 3,[4, 5, 6]]
```

## 条件语句

### if-else 语句

```js
// 通过代码块来避免出现容5易使人误解的缩进
if (i == 0) {
  if (j == 0) {
    console.log('i==0 and j==0')
  } else {
    console.log('i==0 and j!=0')
  }
}
```

### switch-case 语句

```js
// switch-case语句的语法

var s = 'foo'
// 可以在switch表达式中使用字符串值。
// 可以在case表达式中使用和switch表达式类型不同的值。
// s === 0 的值为假，所以将继续进行比较。
switch (s) {
  case 0:
    console.log('not here')
    break
    // 可以在case表达式中使用含有变量的表达式。
    // s === s.length的值为假，所以将继续进行比较。 case s.length:
    console.log('not here')
    break
    // 可以在case表达式中使用方法调用表达式。
    // s === (0).toString()的值为假，所以将继续进行比较。 case (0).toString();
    console.log('not here')
    break
    // 还可以在case表达式中书写这样的表达式。
    // s === 'f' + 'o' + 'o'为真，所以将执行以下的代码。 case 'f' + 'o' + 'o':
    console.log('here')
    break
    // 如果所有的case表达式在等值运算(===)后得到的结果都为假，则执行以下的代码。 default:
    console.log('not here')
    break
}
```

1. 进行相等运算符(===)进行比较
2. case 标签并没有对代码按块 进行分割的功能。因此在一个 case 标签结束执行之后，并不会跳出 switch 语句，需要 break

## 循环语句

### while

```js
// 使用代码块的话就能很容易地理清语句结构
// 循环10次的while语句
var i = 0
while (i < 10) {
  console.log(i)
  i++
  return
}
```

> 使用 break 等来中断循环,continue 跳出当次

要避免出现无限循环，从 while 循环中跳出，可以执行以下操作。

- 保证在循环过程中条件表达式的值将变为假
- 在循环内部使用 break 语句
- 在循环内部抛出异常

### do-while 语句

```js
// do-while语句的例子
do {
  // 不管是否符合条件， 先执行一次
  // 之后，符合条件，继续进入执行
  console.log('in loop')
} while (flag)
```

> 使用 break 等来中断循环,continue 跳出当次

只要稍加调整，这两种情况也都能通过 while 语句来实现。

- 如果循环内的语句不执行一次，条件表达式的求值就没有意义的情况
- 希望确保循环内的语句至少被执行一次的情况

### for 循环

```js
for (var i = 0; i < 10; i++) {
  // 语句
}
```

> 可以使用 return、break 等来中断循环,continue 跳过当次

### for in 循环

> for in 语句是用于枚举对象属性名的循环语句

```js
var obj = { x: 1, y: 3, z: 2 }
for (var k in obj) {
  console.log(k)
}
```

注意：

- 枚举属性的顺序:不是一定的
- 有一些无法被枚举的属性
- 由原型继承而来的属性，会被意外枚举

推荐做法：

```js
var obj = { x: 1, y: 3, z: 2 }
var arr = Object.keys(obj)
for (var i = 0; i < arr.lengt; i++) {
  var name = arr[i]
  console.log(obj[name])
}
```

## 跳出循环

### break 语句

#### 普通用法

```js
// 不通过break语句跳出循环的代码示例
var flag_loop = true
while (flag_loop) {
  省略
  if (跳出循环的条件) {
    flag_loop = false
  }
}
// 通过break语句跳出循环的代码示例
while (true) {
  省略
  if (跳出循环的条件) {
    break
  }
}
```

#### 通过标签跳转

> 借助标签跳转 可以跳出多层循环

```js
// 标签的语法规则
标签字符串: 语句

//eg: 使用标签来同时跳出嵌套的循环
outer_loop: while (true) {
  console.log('outer loop')
  while (true) {
    console.log('inner loop')
    break outer_loop
  }
}
```

#### continue 语句

> 跳过在此之后本次循环内尚未执行的语句,将会跳转至循环的开头。

```js
for (var i = 0; i < 10; i++) {
  if (i % 2 !== 0) {
    continue
  }
  // do something
}
```

### return 语句

> return 语句会中断函数的处理，并将指定的表达式的值作为函数的返回值返回。如果没有指定表达式，函数的返回值将会是 undefined 值

`return 语句`
