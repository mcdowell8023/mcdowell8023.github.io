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

## 异常

throw 语句来抛出异常对象(异常值)

```js
// throw语句的语法规则
throw 表达式
```

借助 try-catch 抛出 捕捉抛出异常

```js
// try-catch-finally结构的语法
try {
  // 正常 流程
} catch (erro) {
  // 捕捉的异常
  throw erro
} finally {
  // 成功、异常 都会 执行
}
```

## 表达式

表达式由 运算符 和 操作数 组成

### 运算符

> 运算符 的优先级

![operator 运算符 的优先级](/images/jsnote/operator.png)

> **两个操作数的运算符被称为双目运算符**，需要在运算符的前后分别书写一个操作数。大部分运算符都是双目运算符，**三目运算符有 1 个**，**其余的都是单目运算符**。三目运算符有 3 个操作数，而单目运算符则只有 1 个操作数。

> 单目运算符又可以根据运算符与操作数的前 后位置关系，分为**前置运算符与后置运算符**。前置运算符是以“运算符 操作数”的顺序书写的，而后置 运算符则是按照“操作数 运算符”的顺序。

### 表达式求值

- 除了含有 **&& 运算符、|| 运算符、?: 运算符**这三个运算符的情况外，**其他的表达式都是首先对操作数进行求值**。
- 操作数按**从左至右的顺序求值**。
- 对于函数方法或是构造函数调用表达式的情况，会在**调用前对参数从左至右求值**。
- **优先对括号内的表达式求值**。
- 如果在对操作数求值的过程中**产生了异常**，则**不会对剩余的操作数进行求值**。
- 对于函数方法或是构造函数调用表达式的情况，如果在对参数进行求值的过程中**发生异常**，则**不会对剩余的参数进行求值**。

### 算数运算符

`+[加法]、-[减法]*[乘法]/[除法]%[取模]、++(前置)++(后置)[自增]、--(前置)、--(后置)[自减]、-(单目)[符号反转]、+(单目)[符号保持不变]`

> ++ 运算符的含义是对操作数加 1。-- 运算符的含义是对操作数减 1。这两个运算符都会重写操作数的值。这类会改写操作数本身的值的运算符，称为**破坏性运算符**

> 赋值运算符是另一种有代表性的破坏性运算符

```js
// 前置运算符的行为
var n = 10
var m = ++n
console.log(m, n) // n变为了11。++n的值是进行了加法之后的值，所以m为11。 11 11
// 后置运算符的行为
var n = 10
var m = n++
console.log(m, n) // n变为了11。++n的值是进行了加法之前的值，所以m为10。 10 11
```

### 相等运算符

- 相等运算 `==`,只比进行**隐式数据类型转换后**的值 是否相等，忽略 两个操作数 本身类型。

- 全等运算 `===`,上来先比较类型是否相等，类型相同后才进行比较，必须是内容一样的，则结果为真。

### 比较运算符

> 如果两个操作数都 是数值，则对这两个**数值的大小**进行比较。如果两个操作数都是字符串型则对**字符串值内容的 Unicode 编码进行大小比较**。

1. 一方为数值，另一方为可以被转换为数值的数据类型的情况，将其**转换为数值类型后再进行大小比较**。
2. 如果操作数中**含有 NaN 则结果为假**。
3. 一方为字符串值，另一方为可以被转换为字符串值的数据类型的情况，将其**转换为字符串值后再对字符串值进行大小比较**。
4. 操作数中有**无法被转换为数值及字符串值的值**，或是**转换结果为 NaN**的情况，运算的**结果为假**。

### in 运算符

指定的属性在指定的对象或其原型链中，则 in 运算符返回 true.

```js
// 数组
var trees = new Array('redwood', 'bay', 'cedar', 'oak', 'maple', null)
0 in trees // 返回true
3 in trees // 返回true
5 in trees // 返回true 只要这个属性确实存在， 仍然返回 true
6 in trees // 返回false
'bay' in trees // 返回false (必须使用索引号,而不是数组元素的值)

'length' in trees // 返回true (length是一个数组属性)

Symbol.iterator in trees // 返回true (数组可迭代，只在ES2015+上有效)

// 内置对象
'PI' in Math // 返回true

// 自定义对象
var mycar = { make: 'Honda', model: 'Accord', year: 1998 }
'make' in mycar // 返回true
'model' in mycar // 返回true
```

### 逻辑运算符

| 运算符 | 说明        | 短路规则                                       |
| ------ | ----------- | ---------------------------------------------- |
| !      | 逻辑非(NOT) | 无(单目运算符)                                 |
| &&     | 逻辑与(AND) | 若左操作数的值为假，则不再对右操作数进行求值。 |
| \|\|   | 逻辑或(OR)  | 若左操作数的值为真，则不再对右操作数进行求值。 |

&& 运算和 || 运算有一个重要的性质——**短路求值**。运算前仅会**先对左边的操作数进行求值**。

```js
var n = 1 && 2
console.log(n) // 2

var n = 0 && 2
console.log(n) // 0

var n = 0 || '--'
console.log(n) // --

var n = 'type' || 'default'
console.log(n) // type
```

### 条件运算符（三目运算符）

- 条件运算符是唯一的三目运算符。由于三目运算符只有这一个，所以有时也会直接把条件运算符称 为三目运算符。
- 与 && 运算符和 || 运算符一样，条件运算符也有短路求值的特性。
- 基本可以理解为`if else`的缩写，把`?`看作 `if`,`:`看作`else`
  `条件表达式 ? 表达式1 : 表达式2`

常用示例

```js
// 这是 一个处理数字 小于两位数时候，的补0 操作函数
function formatZero(num) {
  if (isNaN(num)) {
    return '--'
  }
  // 在获得计算结果后，进行返值
  return num > 10 ? '' + num : '0' + num
}
```

### 位运算符

| 运算符 | 说明                              |
| ------ | --------------------------------- |
| &      | 按位与(AND)                       |
| \|     | 按位或(OR)                        |
| ^      | 按位异或(XOR)                     |
| <<     | 左移                              |
| >>     | 右移(最左位保持原符号不变)        |
| >>>    | 无符号右移(最左位被置为 0)        |
| ~      | 单目运算符。按位取反，取 1 的补码 |

```js
// 右侧移动 1位 除以2^1(2的1次方); 移动 2位 除以 2^2(2的2次方);....
72 >> 1 // 36(72/2)
72 >> 2 // 18(72/2^2)
// 左侧移动 1位 乘以2^1(2的1次方); 移动 2位 乘以 2^2(2的2次方);....
72 << 2 // 288 (72*2^2)
72 << 3 // 576 (72*2^3)
```

### 赋值运算符

用于对变量赋值的 = 运算符是赋值运算符

```js
var a = 1
console.log(a) // 1
// 多次赋值 从右到左
x = y = z = 0
console.log(x, y, z) // 0 0 0

z += 1
y -= 1
console.log(z, y) // 1,-1
```

### new 运算符

new 运算符**创建一个用户定义的对象类型 的 实例**或具**有构造函数的内置对象 的 实例**。new 关键字会进行如下的操作：

1. 创建一个空的简单 JavaScript 对象（即{}）；
2. 链接该对象（即设置该对象的构造函数）到另一个对象 ；
3. 将步骤 1 新创建的对象作为 this 的上下文 ；
4. 如果该函数没有返回对象，则返回 this。

```js
function Car(make, model, year) {
  this.make = make
  this.model = model
  this.year = year
}

const car1 = new Car('Eagle', 'Talon TSi', 1993)

console.log(car1.make)
```

### delete 运算符

delete 操作符会从某个对象上移除指定属性。成功删除的时候回返回 true，否则返回 false。

> 注意： delete 对象上没有的属性，依然会返回 true
> 【成功与否 主要看 Object.defineProperty 设置该属性 的 configurable 是否 true】

```js
const object1 = {}

Object.defineProperty(object1, 'property1', {
  configurable: false, // 如果设为true 就可以 delete object1.property1 返回 true
  value: 42,
  writable: false,
})
object1.name = 'jack'
object1.tag = null

delete object1.name // true
delete object1.tag // true
delete object1.id // true

delete object1.property1 // false
```

### void 运算符

**void 是 undefined 类型的单目运算符**。无论向其传递什么操作数，其运算**结果都会是 undefined 值**。

> 由于 js 的特性 undefined 不是保留字，容易被篡改，所以建议使用 `void 0` 代替 undefined 进行类型判断

#### 一个 void 的常见用法

```html
<a href="javascript:void(document.form.submit())"
  >发送HTML表单数据但不跳转页面</a
>
<!-- 
  为了阻止标 签 a 跳转页面，需要将 href 属性中表达式的值强制设为 undefined 值。
对此最为简单的惯用方法就是通 过 void 运算来实现。 
-->
```

### 逗号(,)运算符

逗号运算符(,)是一个双目运算符，其作用为**依次对其左操作数与右操作数求值**。
逗号运算符的**运算结果是其右操作数的值**，也就是说其结果的类型取决于所使用的操作数

## 数据类型的判断

> 本节建议看完 全部 Object 对象章节，再回过头重看一遍

### typeof 运算符

typeof 一元运算符，用来返回操作数类型的字符串

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

typeof function () {} // "function"  -- 由于特殊考虑，确实需要将function 与 object区分开来

typeof [1, 2, 3] // "object" -- 可是数组 不如你所预想的那样
typeof /abc/g // "object"
typeof new RegExp('meow') // "object" -- 遇到检测数组的同样问题
```

> 正如你看到的，typeof 返回的，不一定都是你想看到的。【因为 array、正则表达式、函数等，除了原始值，都是对象】

实际情况一览表：

| Value              | Class    | Type                          |
| ------------------ | -------- | ----------------------------- |
| "foo"              | String   | string                        |
| new String("foo")  | String   | object                        |
| 1.2                | Number   | number                        |
| new Number(1.2)    | Number   | object                        |
| true               | Boolean  | boolean                       |
| new Boolean(true)  | Boolean  | object                        |
| new Date()         | Date     | object                        |
| new Error()        | Error    | object                        |
| [1,2,3]            | Array    | object                        |
| new Array(1, 2, 3) | Array    | object                        |
| new Function("")   | Function | function                      |
| /abc/g             | RegExp   | object (function in Nitro/V8) |
| new RegExp("meow") | RegExp   | object (function in Nitro/V8) |
| {}                 | Object   | object                        |
| new Object()       | Object   | object                        |

> 你可以看到：除了被正常的个数据类型对象外，显示为 `object` 的还有 js 内置对象 `RegExp(正则对象)`,`Array 对象`,`Error对象`,`Date 日期对象`

那么我们用什么判断类型，数组是数组，正则是正则，字符串是字符串呢？

### instanceof 运算符

用法

`object instanceof constructor`

> 运算符用于检测**构造函数的 prototype** 属性是否出现在某个**实例对象的原型链上**。
> 【看起来可以完美进行类型判断】但是，**只有在比较自定义的对象时才有意义**。 如果**用来比较内置类型用处不大**

#### 比较 自定义对象

```js
//
function Foo() {}
function Bar() {}
Bar.prototype = new Foo()

new Bar() instanceof Bar // true
new Bar() instanceof Foo // true

// 如果仅仅设置 Bar.prototype 为函数 Foo 本身，而不是 Foo 构造函数的一个实例
Bar.prototype = Foo
new Bar() instanceof Foo // false
```

#### 比较 比较内置类型

```js
  /abc/g instanceof RegExp // true
  new Date() instanceof Date // true

  '123' instanceof Array // false
  123 instanceof Array // false
  [] instanceof Array // true
  ({}) instanceof Array // false  // 不加（） 会报错
  // 然而 你以为完美了？ 不然。。。
  [] instanceof Object // true
  /abc/g instanceof Object // true
  // 比较内置类型
  new String('foo') instanceof String; // true
  new String('foo') instanceof Object; // true
  'foo' instanceof String; // false
  'foo' instanceof Object; // false

```

注意：**用来比较内置类型用处不大**

1. **基本类型值**（undefined,null,'23df',123,true） `instanceof Object(String、Number)` 也**都是 false**
   - 因为检查原型链会找到 undefined
   - 其实按照用法，要求进行两个对象 进行比较 ，而这种比较方式本身就是错误的
2. js 所有 对象 都派生 Object ，所以 任何**对象** `instanceof Object` **都是 true**
3. `!(mycar instanceof Car)` 与 `!mycar instanceof Car` 不同，后者 `会先 转换 为 布尔值，然后进行instanceof判断，会一直显示 false`
4. 使用 `arr instanceof Array`判断是不是数组,仅限于在当前 JavaScript 上下文的 Array 对象 相同。

#### instanceof 操作符**应该仅仅用来比较来自同一个 JavaScript 上下文的自定义对象**

> instanceof 问题在于，它**假定了单一的全局执行环境**。如果**网页中包含多个框架，那实际上就存在两个以上不同的全局执行环境**，
> 从而存在两个以上不同版本的 Array 构造函数。如果你从一个框架向另一个框架传入一个数组，那么传入的数组与在第二个框架中原生创建的数组分别具有各自不同的构造函数。
> 为了解决这个问题，尽量使用 Array.isArray()方法，这个方法的目的是确认某个值是否是数组，而不管它是在哪个全局执行环境中创建的。
> 支持 Array.isArray()的浏览器：IE9+、Firefox 4+、Safari 5+、Opera 10.5+和 chrome。

那么如何一劳永逸的判断数据类型？

#### 对象的类定义

其实我们要的，是检测对象的 的内部属性 [[Class]] 的值 （对象的 类定义）；

> JavaScript 标准文档中定义: [[Class]] 的值只可能是下面字符串中的一个： Arguments, Array, Boolean, Date, Error, Function, JSON, Math, Number, Object, RegExp, String.

为了获取对象的 [[Class]]，我们需要使用定义在 Object.prototype 上的方法 toString。

```js
Object.prototype.toString.call([]) // "[object Array]"
Object.prototype.toString.call({}) // "[object Object]"
Object.prototype.toString.call(4) // "[object Number]"
Object.prototype.toString.call('acdf') // "[object String]"
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

> 记住 一劳永逸的的判断 变量类型：`Object.prototype.toString.call`

- 参考 1：[typeof 和 instanceOf 的区别](https://segmentfault.com/a/1190000000730982)
- 参考 2：[为什么用 Object.prototype.toString.call(obj)检测对象类型?](https://www.cnblogs.com/youhong/p/6209054.html)

---

[js 从原始数据类型到深浅拷贝](https://juejin.im/post/5ed3664de51d45783e17b12a?utm_source=gold_browser_extension)
