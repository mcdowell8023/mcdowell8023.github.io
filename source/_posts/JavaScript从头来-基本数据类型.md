---
title: JavaScript从头来-基本数据类型
comments: true
mathjax: true
categories:
  - 基础知识
tags:
  - js基础
  - 追本溯源
  - 笔记
date: 2020-03-29 06:47:24
---

> 本篇 是 JavaScript 从头来 的第二篇。
> 数据类型，在面试中经常问到。 判断数据类型也是日常开发中的常见操作。由此可见，数据类型是多么重要。

## 数据类型

#### ECMAscript 的数据类型：

##### 简单的数据类型（基本数据类型）【7 种】

```js
  String、Boolean、Null、Undefined、Number、 Symbol、BigInt
  // Symbol 是 ES6 引入了一种新的原始数据类型，表示独一无二的值
  // BigInt 也是 新增类型 用于表示比较大的 正数
  // 后两种为ES6新增类型 放在最后说

```

##### 复杂数据类型【1 种】

```js
Objecet // 本质由一组无序的名值队组成
// Array、Function 属于 object 类型
```

> ECMAscript 数据类型具有动态性【弱类型】【变量可以从一个类型变成另外的类型】

### 数据类型的判断

#### typeof 操作符

typeof 是检测数据类型的，返回字符串

- 'undefined' -- 未定义
- 'boolean' -- 布尔值
- 'string' -- 字符串
- 'number' -- 数值
- 'object' -- 对象或者 null [ null 被认为是 空对象的引用 ]
- 'function' -- 函数

> 技术角度说，函数不是一种数据类型，是对象，但是函数有一些特殊属性。因此需要 将函数 与对象区分开

```js
typeof true // "boolean"
typeof {} // "object"
typeof typeof 11 // "string"

typeof function() {} // "function"  -- 由于特殊考虑，确实需要将function 与 object区分开来
typeof [1, 2, 3] // "object" -- 可是数组 不如你所预想的那样
```

[typeof 和 instanceOf 的区别](https://segmentfault.com/a/1190000000730982)

### String

    String类型由零个或者多个 **16位Unicode字符** 组成的字符序列。`''`、`""` 单引号、双引号均可。

1. 转移序列【用于表示非打印字符、或者具有其他用途的字符】
   字面量| 含义
   ---|---
   \0 | 空字符
   \ ' | 单引号
   \ " | 双引号
   \ \ | 反斜杠
   \n | 换行(LF) 【将光标“垂直”移动到下一行，而并不移动到下一行的开头】
   \r | 回车(CR)【将光标移动到当前行的开头】
   \v | 垂直制表符
   \t | 水平制表符 【缩进】
   \b | 退格
   \f | 换页
   \xXX| 用十六进制代码表示 一个字符 如： \x42 表示 字符 B
   \uXXXX| 用十六进制代码表示 一个 Unicode 字符 如： \u03a3 表示 Σ
2. 字符的几点
   - 字符串一旦创建无法改变
   - 修改变量的字符串需要采取覆盖方式 `var str ='qwer'; str = str+'000';`
   - 长字符串
   ```js
   // 使用 + 进行拼接
   let longString =
     'This is a very long string which needs ' +
     'to wrap across multiple lines because ' +
     'otherwise my code is unreadable.'
   // 使用 \ 连接
   let longString =
     'This is a very long string which needs \
   to wrap across multiple lines because \
   otherwise my code is unreadable.'
   ```
   - ES6 新增 模版字符串
   ```js
   var name = 'mcdowell'
   console.log(`hi!${name}`) //  hi!mcdowell
   ```
3. 转换为字符串

[javascript 中的装箱和拆箱操作](https://www.cnblogs.com/wenber/p/3628944.html)
