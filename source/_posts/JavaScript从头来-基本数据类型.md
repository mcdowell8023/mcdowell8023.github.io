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

### String

String 类型由零个或者多个 **16 位 Unicode 字符** 组成的字符序列。`''`、`""` 单引号、双引号均可。

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

---

### 数据类型的判断

#### typeof 操作符

typeof 是检测数据类型的，返回 操作数类型 的字符串

- 'undefined' -- 未定义
- 'boolean' -- 布尔值
- 'string' -- 字符串
- 'number' -- 数值
- 'function' -- 函数
- 'object' -- 对象或者 null [ null 被认为是 空对象的引用 ]

> 技术角度说，函数不是一种数据类型，是对象，但是函数有一些特殊属性。因此需要 将函数 与对象区分开

```js
typeof true // "boolean"
typeof {} // "object"
typeof typeof 11 // "string"

typeof function() {} // "function"  -- 由于特殊考虑，确实需要将function 与 object区分开来

typeof [1, 2, 3] // "object" -- 可是数组 不如你所预想的那样
typeof /abc/g // "object"
typeof new RegExp('meow') // "object" -- 遇到检测数组的同样问题
```

> 正如你看到的，typeof 返回的，不一定都是你想看到的。【因为 array、正则表达式、函数等，除了原始值，都是对象】

#### 对象的类定义

其实我们要的，是检测对象的 的内部属性 [[Class]] 的值 （对象的 类定义）；

> JavaScript 标准文档中定义: [[Class]] 的值只可能是下面字符串中的一个： Arguments, Array, Boolean, Date, Error, Function, JSON, Math, Number, Object, RegExp, String.

为了获取对象的 [[Class]]，我们需要使用定义在 Object.prototype 上的方法 toString。

```js
Object.prototype.toString.call([]) // "[object Array]"
Object.prototype.toString.call({}) // "[object Object]"
Object.prototype.toString.call(4) // "[object Number]"
// 高级浏览器表现
Object.prototype.toString.call(null) // "[object Null]"
Object.prototype.toString.call(undefined) // "[object Undefined]"

// IE8
Object.prototype.toString.call(null) // "[object Object]"
Object.prototype.toString.call(undefined) // "[object Object]"
// 通用方法 通常不对null undefined 进行判断
function is(type, obj) {
  var clas = Object.prototype.toString.call(obj).slice(8, -1)
  return obj !== undefined && obj !== null && clas === type
}

is('String', 'test') // true
is('String', new String('test')) // true
```

- 敲黑板
  > 辨别到底是不是数组的方法 ：使用 Array.isArray 或者 Object.prototype.toString.call 方法

那么为什么是 `Object.prototype.toString` 而不是直接使用 toString 方法。因为，array 、date 等 对象都是派生自 `Object`,对其进行实例化的时候，已经改写了 toString 方法。

- 参考 1：[typeof 和 instanceOf 的区别](https://segmentfault.com/a/1190000000730982)
- 参考 2：[为什么用 Object.prototype.toString.call(obj)检测对象类型?](https://www.cnblogs.com/youhong/p/6209054.html)
