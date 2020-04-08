---
title: JavaScript从头来-数据类型
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

### ECMAscript 的数据类型

#### 简单的数据类型（基本数据类型）【7 种】

```js
  字符串型、数值型、布尔型、null型、undefined型、 Symbol型、BigInt型
  // Symbol 是 ES6 引入了一种新的原始数据类型，表示独一无二的值
  // BigInt 也是 新增类型 用于表示比较大的 正数
  // 后两种为ES6新增类型 放在最后说
```

#### 复杂数据类型（引用类型）【1 种】

```js
Objecet类型 // 本质由一组无序的名值队组成
// 几种基本数据类型以外的都被称为 Objecet 【 其实是个合集，并不单纯指 {} 】
// Array、Function 属于 object 类型
```

> ECMAscript 数据类型具有动态性【弱类型】【 变量可以从一个类型变成另外的类型，不存在变量类型的概念 】

---

> 基本数据类型 的 实例 被称作《值》，Object 类型的实例 被称为 《对象》。

### 字符串

#### 字符串字面量（字符串型）

字符串 类型由零个或者多个 **16 位 Unicode 字符** 组成的字符序列。`''`、`""` 单引号、双引号均可。

##### 转译序列【用于表示非打印字符、或者具有其他用途的字符】

| 字面量 | 含义                                                            |
| ------ | --------------------------------------------------------------- |
| \0     | 空字符                                                          |
| \ '    | 单引号                                                          |
| \ "    | 双引号                                                          |
| \ \    | 反斜杠                                                          |
| \n     | 换行(LF) 【将光标“垂直”移动到下一行，而并不移动到下一行的开头】 |
| \r     | 回车(CR)【将光标移动到当前行的开头】                            |
| \v     | 垂直制表符                                                      |
| \t     | 水平制表符 【缩进】                                             |
| \b     | 退格                                                            |
| \f     | 换页                                                            |
| \xXX   | 用十六进制代码表示 一个字符 如： \x42 表示 字符 B               |
| \uXXXX | 用十六进制代码表示 一个 Unicode 字符 如： \u03a3 表示 Σ         |

##### 字符串的运算

1. 字符串一旦创建无法改变、修改变量的字符串需要采取覆盖方式 `var str ='qwer'; str = str+'000';`
2. 使用 + 、+= 进行拼接
3. 长字符串拼接

   - 普通拼接

   ```js
   // 使用 + 进行拼接
   let longString =
     'This is a very long string which needs ' +
     'to wrap across multiple lines because ' +
     'otherwise my code is unreadable.'

   longString += '还可使用+=方式'

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

4. 字符串的比较

```js
var str1 = '0'
var str2 = '22'
var num = str1 + str2 - 0 // 一种从从 字符串转 换为 数字 的方式
var str3 = str1 + str2

console.log(str1 == str2) // false
console.log(str1 === str2) // false
console.log(str3 == num) // true
console.log(str3 === num) // false

var name1 = 'jack'
var name2 = 'mcdowell'
var name3 = '张三'
var name4 = '李四'
console.log(name2 >= name1) // true
console.log(name2 < name1) // false
console.log(name2 > name3) // false
console.log(name3 < name4) // true
```

比较运算基于 Unicode 字符的编码值（编码位置）【实际中只有英语单词的比较是有意义的】

- 英文字母是字典序（abc 顺序）
- 英文字母的大写 排在小写字母前
- 字符串 比较第一个字符
- 数字和符号在英文字母之前（个别符号在字母之后）
- 中文字符情况更为复杂，基于 《康熙字典》的部首顺序及壁画顺序进行排序的

#### 字符串类（String 类）

##### 字符串对象

String 类 其实就是 String() 方法，我们可以借助 `new`【 构造函数调用 】 进行实例化 得到 字符串对象

```js
  var stringObject = new String('123') // 生成字符串对象  也就是装箱操作
  typeOf stringObject // object
  var stringValue = stringObject.valueOf() // 得到字符串对象的值 也就是拆箱操作

```

> **装箱**: 把基本数据类型转换为对应的引用类型的操作称为装箱，把引用类型转换为基本的数据类型称为拆箱
> **拆箱**: 将引用类型对象转换为对应的值类型对象，它是通过引用类型的 valueOf()或者 toString()方法来实现的。如果是自定义的对象，你也可以自定义它的 valueOf()/tostring()方法，实现对这个对象的拆箱。
> 摘自：[javascript 中的装箱和拆箱操作](https://www.cnblogs.com/wenber/p/3628944.html)

##### 字符串型 与 String 类 进行的隐式转换

字符串型 与 String 类 之间存在隐式转换，即 从 字符串型到 String 类 是 装箱操作，从 String 类 到 字符串型 是拆箱操作。

```js
'123'.length // 3  要得到字符串的长度，其实是先进行了 隐式转换 装箱操作 才得到的结果

// '123'其实只是字面量，他自己本身是没有 length 的方法。那么 实际是如何运行的呢
// 字符串 ‘123’ 将会先被隐式转换 成 字符串对象
var stringObject = new String('123') // 生成字符串对象  也就是装箱操作
// 然后 读取 字符串对象 下的 length
stringObject.length // 3

typeof stringObject
```

###### 通过 '+' 进行隐式转换

```js
var stringObject = new String('123') // 生成字符串对象 也就是装箱操作

stringObject + '789' // '123789'  把 字符串对象 进行 隐式转换

// 常见使用的隐式转换

456 + '' // '456'
// 过程 => new String(456).valueOf() + '' // 把 字符串对象 进行 隐式转换(valueOf()) 后进行拼接
```

###### 字符串与字符串对象

```js
var str1 = new String('abc')
var str2 = new String('abc')

str1 == 'abc' // true
str2 == 'abc' // true  在左右两边类型不想等时候，会进行隐式转换成（调用对象的的 valueOf）变成字符串 后进行比较
str2 === 'abc' // false 类型不一样
str1 == str2 // false  == 虽然字符串相等，但是并非引用了同一个对象， 所以是false
str1 === str2 // false  == 虽然字符串相等，但是并非引用了同一个对象， 所以是false

// 实际中不推荐 这样使用
str1 + '' == str2 + '' // true
str1 + '' === str2 + '' // true
```

> 为了避免 字符串 与 字符串对象 混用，造成混乱，使用字符串隐式转换即可满足日常

#### 字符串类 的方法

> 建议在控制台中输入 new String() 得到 String 进行属性查看，同步理解

##### 函数或者构造函数

- String(value) : 将参数转换为字符串类型
- new String(value) : 得到一个 Sting 实例

##### 属性

- fromCharCode([char0,...]): 可接受一个指定的 Unicode 值，然后返回一个字符串[只接受 Unicode 的数字]; `String.fromCharCode(72,69,76,76,79)// HELLO`
- length: 值为 1
- prototype: 用于原型链

##### prototype 对象所具有的部分属性

| 字面量                                                   | 含义                                                                                                                                                                                                             |
| -------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `String.prototype.charAt(pos)`                           | 返回下标 pos 对应位置的 的长度为 1 的字符串，从 0 开始，如果超过了，返回空字符串                                                                                                                                 |
| String.prototype.charCodeAt(pos)                         | 返回下标 pos 对应位置 字符 编码，如果超过了下标范围，返回 NAN                                                                                                                                                    |
| `String.prototype.concat([str，...])`                    | 和参数字符串连接后返回新的字符串                                                                                                                                                                                 |
| String.prototype.endsWith(endStr)                        | 用来判断 字符串 是否是以 endStr 结尾，返回 true 或 false ｜                                                                                                                                                      |
| `String.prototype.includes(str,start=0)`                 | 根据第一个参数查找字符串，根据情况返回 true 或 false；第二个参数，指定检索开始位置，可不传，默认 0                                                                                                               |
| `String.prototype.indexOf(str,start=0)`                  | 根据第一个参数查找字符串，返回下标，如果没有返回-1；第二个参数，指定检索开始位置，可不传，默认 0                                                                                                                 |
| `String.prototype.lastIndexOf(searchValue[, fromIndex])` | 从尾到头地检索字符串，看它是否含有子串 searchValue,返回对应的 index,找不到返回-1， 从 fromIndex 位开始 向左（向开始位） 查找 字符串 【区分大小写】                                                               |
| String.prototype.localeCompare()                         | 用于字符串比较，用本地特定的顺序来比较两个字符串。如果 stringObject 大于 target，则该方法返回大于 0 的数。`stringObject.localeCompare(target) // 如果两个字符串相等，或根据本地排序规则没有区别，该方法返回 0。` |
| `String.prototype.match(regexp)`                         | 返回匹配正则表达式 regexp 的结果                                                                                                                                                                                 |
| `String.prototype.matchAll()`                            | 方法返回一个包含所有匹配正则表达式的结果及分组捕获组的迭代器                                                                                                                                                     |
| String.prototype.padEnd(length,str)                      | 用 str 从末尾 填充字符串到指定长度[如果填充长度小于当前长度，不进行填充]                                                                                                                                         |
| String.prototype.padStart(length,str)                    | 用 str 从头 填充字符串到指定长度[如果填充长度小于当前长度，不进行填充]                                                                                                                                           |
| String.prototype.repeat(count)                           | 重复 count（正整数） 次返回 字符出副本[-1:报错，1：不重复，2:重复两次。。。]                                                                                                                                     |
| `String.prototype.replace(searchValue,replaceValue)`     | 将 searchValue（正则表达式或者字符串值）替换为 replaceValue（字符串或者函数）后返回经过替换后的字符串                                                                                                            |
| `String.prototype.search(regexp)`                        | 返回匹配正则表达式 regexp 的位置的下标                                                                                                                                                                           |
| `String.prototype.slice(start，end)`                     | 将参数 start 开始至 end 结束的字符串部分作为新的字符串值返回。如果 start 和 end 是负数，则返回从末尾逆向起数的下标值。                                                                                           |
| `String.prototype.split(separator,howmany)`              | 依据 separator 字符串或正则表达式 进行分割为数组，howmany 小于数组 length 按 howmany 返回对应长度数组，超过 length 返回 length                                                                                   |
| String.prototype.startsWith(str,start)                   | 判断当前字符串是否以另外一个给定的子字符串开头，并根据判断结果返回 true 或 false。 start:从 start 位开始                                                                                                         |
| `String.prototype.substring(indexStart[, indexEnd])`     | 返回一个截取 开始索引 到 结束索引的字符串, indexStart：需要截取的第一个字符的索引（包含位）。indexEnd：可选。一个 0 到字符串长度之间的整数（不包含）。                                                           |
| String.prototype.toLowerCase()                           | 将字符串转为小写                                                                                                                                                                                                 |
| String.prototype.toString()                              | 返回指定对象的字符串形式                                                                                                                                                                                         |
| String.prototype.toUpperCase()                           | 返回大写字符串                                                                                                                                                                                                   |
| String.prototype.trim()                                  | 去除字符串的头尾空格                                                                                                                                                                                             |
| String.prototype.trimEnd()                               | 去除字符串的尾部空格 trimRight 是别名                                                                                                                                                                            |
| String.prototype.trimStart()                             | 去除字符串的头部空格 trimLeft 是别名                                                                                                                                                                             |
| String.prototype.valueOf()                               | 返回对象的原始值                                                                                                                                                                                                 |

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

typeof function () {} // "function"  -- 由于特殊考虑，确实需要将function 与 object区分开来

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
