---
title: JavaScript从头来2.1-数据类型
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

#### 简单的数据类型（基本数据类型）【 5 + 2 种 】

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

#### 字符串字面量（字符串值）

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

#### 字符串包装类型（String 类）

##### 字符串对象

String 类 其实就是 String() 方法，我们可以借助 `new`【 构造函数调用 】 进行实例化 得到 字符串对象

```js
  var stringObject = new String('123') // 生成字符串对象  也就是装箱操作
  typeOf stringObject // object
  var stringValue = stringObject.valueOf() // 得到字符串对象的值 也就是拆箱操作

```

> **装箱**: 把基本数据类型转换为对应的引用类型的操作称为装箱，把引用类型转换为基本的数据类型称为拆箱
> **拆箱**: 将引用类型对象转换为对应的值类型对象，它是通过引用类型的 valueOf()或者 toString()方法来实现的。如果是自定义的对象，你也可以自定义它的 valueOf()/tostring()方法，实现对这个对象的拆箱。
> js 中提供 三种 包装类型，分别为 String 包装类型、Boolean 包装类型、Number 包装类型
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

> 建议在控制台中输入 new String() 得到 String 进行属性查看

##### 常用方法分类(String.prototype 上的方法)

1. 字符方法 `charAt`和 `charCodeAt` 、`[]`

```js
// charAt 根据 下标 返回 对应位置的 的长度为 1 的字符串，从 0 开始，如果超过了，返回空字符串
var stringValue = 'hello world'
console.log(stringValue.charAt(1)) //"e"
// charCodeAt 根据 下标 返回 对应位置的 的长度为 1 的字符串 的字符 编码，从 0 开始，如果超过了，返回NaN
console.log(stringValue.charCodeAt(1)) //返回字符编码"101"

// string 有length 自然也有 []方式
console.log(stringValue[2]) // "l"
```

2. 字符串操作方法

**拼接 concat**

```js
// 用于 拼接字符串 实际使用 '+' 进行拼接更多
var stringValue = 'hello '
var result = stringValue.concat('world', '!')
alert(result) // "hello world!"
alert(stringValue) // "hello"
```

**取出新字符串 slice、substr、 substring**

> 取出新字符串 不影响 原来的字符串

```js
var stringValue = 'hello world'
// 按照 参数 start 开始至 end 结束 取出新的字符串。如果 start 和 end 是负数，则返回从末尾逆向起数的下标值
alert(stringValue.slice(3)) //"lo world"
alert(stringValue.substring(3)) //"lo world"
alert(stringValue.substr(3)) //"lo world"
// 截取到 第七位 但是不包含第七位
alert(stringValue.slice(3, 7)) //"lo w"
// 截取到 第七位 但是不包含第七位
alert(stringValue.substring(3, 7)) //"lo w"

// 从3位向后数，截取 7个字符
alert(stringValue.substr(3, 7)) //"lo worl"
```

- 第一个参数 为 负数：
  > slice()和 substr() 在接收一个负数时，会经过计算 （length + 负数（如：-3））后，进行操作
  > substring() 直接忽略 负数 为 0，返回全部字符。
- 第二个参数 为 负数：
  > slice() 从数组尾部开始算起的元素，会经过计算 （length+ 1 + 负数（如：-3））后，进行操作
  > substring() 直接忽略 负数 为 0, 进行操作（类似余 start 与 end 位置互换）
  > substr() 直接忽略 负数 为 0，返回全空字符。
- 示例：

```js
var stringValue = 'hello world'
alert(stringValue.slice(-3)) //"rld"
alert(stringValue.substring(-3)) //"hello world"
alert(stringValue.substr(-3)) //"rld"
alert(stringValue.slice(3, -4)) //"lo w"
alert(stringValue.substring(3, -4)) //"hel"
alert(stringValue.substr(3, -4)) //""
```

3. 字符串位置 `indexOf()`和`lastIndexOf()`

> indexOf 从头部 查找字符串返回 index，没找到返回 -1； 第二参数 ，指定 从头部 开始的查找位置
> lastIndexOf 从尾部 查找字符串返回 index，没找到返回 -1； 第二参数 ，指定 从尾部 开始的查找位置

```js
var stringValue = 'hello world'
alert(stringValue.indexOf('o')) //4
alert(stringValue.lastIndexOf('o')) //7
alert(stringValue.indexOf('o', 6)) //7
alert(stringValue.lastIndexOf('o', 6)) //4
```

4. trim() 返回 去掉首尾 空格
5. 字符串大小写转换

```js
var stringValue = 'hello world'

alert(stringValue.toUpperCase()) //"HELLO WORLD"
alert(stringValue.toLowerCase()) //"hello world"

// 本地 化的大小写 转换
alert(stringValue.toLocaleUpperCase()) //"HELLO WORLD"
alert(stringValue.toLocaleLowerCase()) //"hello world"
```

6. 字符串模式匹配方法

**match**

- 接收正则 或者 要匹配的字符串
- 在字符串内检索指定的值，或找到一个或多个正则表达式的匹配
- 类似 indexOf() 和 lastIndexOf()，但是它返回指定的值，而不是字符串的位置
- [与 RegExp 对象的 exec()相同]

```js
var str = 'cat, bat, sat, fat'
// 仅匹配以第一位 的 ，返回含有 index ，值 等信息的特殊数组
str.match(/.at/) // ["cat", index: 0, input: "cat, bat, sat, fat", groups: undefined]
// 全局匹配 是返回全部数组
str.match(/.at/g) // ["cat", "bat", "sat", "fat"]
```

**search**

- 接收正则 或者 要匹配的字符串
- 在字符串内检索指定的值，或找到一个正则表达式的匹配 返回 第一次出现的位置，找不到返回 -1
- 类似 indexOf()
- 借助 正则 中的 i 实现忽略大小写匹配

```js
var str = 'cat, bat, sat, fat'
str.search('bat') // 5
str.search(/.at/) // 0
str.search(/.at/g) // 0
str.search(/.bt/) // -1
```

**replace**

- 接收两个参数 [0]要替换的 字符串或者正则 ；[1]替换内容 或者 函数返回 的替换内容
- 用于在字符串中用一些字符替换另一些字符
- 或替换一个与正则表达式匹配的子串

```js
  var str = 'cat, bat, sat, fat'
  str.replace('at,'123') // "c123, bat, sat, fat"
  str.replace(/.at/,'123') // "123, bat, sat, fat"
  str.replace(/.at/g,'$&_123') // "cat_123, bat_123, sat_123, fat_123"
```

[更多](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/String/replace#%E4%BD%BF%E7%94%A8%E5%AD%97%E7%AC%A6%E4%B8%B2%E4%BD%9C%E4%B8%BA%E5%8F%82%E6%95%B0)

**split**

- 接收两个参数 [0]要替换的 字符串或者正则 ；[1] 可选参数--指定返回的数组的最大长度
- 方法用于把一个字符串分割成字符串数组

```js
var str = 'How are you doing today?'

console.log(str.split(' ')) // ['How','are','you','doing','today?']
console.log(str.split('')) // ["H", "o", "w", " ", "a", "r", "e", " ", "y", "o", "u", " ", "d", "o", "i", "n", "g", " ", "t", "o", "d", "a", "y", "?"]
console.log(str.split(' ', 3)) // ["How", "are", "you"]

var htmlStr = '<p>213</p>'
htmlStr.split(/\<|\>/) // ["", "p", "213", "/p", ""]
```

**localeCompare**

- 用本地特定的顺序来比较两个字符串。
- 要以本地特定的顺序与 stringObject 进行比较的字符串
- 返回值 ：如果 stringObject 小于 target，则 localeCompare() 返回小于 0 的数。如果 stringObject 大于 target，则该方法返回大于 0 的数。如果两个字符串相等，或根据本地排序规则没有区别，该方法返回 0

```js
var stringValue = 'yellow'
alert(stringValue.localeCompare('brick')) //1
alert(stringValue.localeCompare('yellow')) //0
alert(stringValue.localeCompare('zoo')) //-1
```

排序示例：

```js
// 将用本地特定排序规则对字符串数组进行排序
var str = 'asdfsdwerqvzfvadfas'.split('')
str.sort(function (a, b) {
  return a.localeCompare(b)
})
// 结果： ["a", "a", "a", "d", "d", "d", "e", "f", "f", "f", "q", "r", "s", "s", "s", "v", "v", "w", "z"]
```

### Number 型

> 在 javascript 中，数值的内部结构为 64 位浮点小数，即时是整数，也是由浮点小数形式存在的。因为所有数值都是浮点小数，所以运行效率多少会有些下降。

#### 不同的方式 表示不同的 字面量

1. 十进制：常见 79 、12 等等。
2. 八进制: 第一位必须是 0，然后是八进制序列（0 ～ 7）,如果大于 7 则被认为是 十进制。

   ```js
   var octNum1 = 070 // 8进制 的 56
   var octNum2 = 0621 // 8进制 的 401
   var octNum3 = 079 // 无效的8进制 ---解析为 79

   // 8进制 转换 为 10进制
   0621 // =>
   ;(6 * 8) ^ (2 + 2 * 8) ^ (1 + 1 * 8) ^ 0
   ```

> 八进制 字面量在 严格模式下，是无效的，会导致报错

1. 十六进制: 前两位必须是 0x,然后是 16 进制序列（0 ～ 9、A ～ F）[A ～ F 可大写，可小写]

   ```js
   var hexNum1 = 0xa // 16进制 的 10
   var hexNum2 = 0x1f // 16进制 的 31
   var hrexNum2 = 0x200 // 16进制的 512
   // 16进制 转换 为 10进制
   0x200 // =>
   ;(2 * 16) ^ (2 + 0 * 16) ^ (1 + 0 * 16) ^ 0
   ```

#### 计算

> 在进行算数计算的时候，八进制和十六进制表示的数值都会被转换为 十进制数值。

```js
// 两个八进制相加
071 + 022 // = 75 （结果为 十进制 ）

/*
    【 计算过程：
     57 + 18 = 75
    或：
    个位：1 + 2 => 3
    十位： 7 + 2 => 9 ,8进1 余1，
    结果是： 0113 （十进制的 75 ） 】
    */

// 两个十六进制相加
0xa2 + 0xff // 417 (结果为 十进制)
/*
    【计算过程：
    个位： 2 + F => 17，余1 进 1
    十位： A + F + 1 => 26 , 进1 余10，
    结果是：0x1A1 （十进制的 417 ）】
    */
```

> js 其实不管是整数还是浮点数 都是使用 IEEE754【二进制浮点数算术标准】 进行存储 运算的

#### 浮点数

浮点数必须包含小数点，而且小数点后至少一位。

```js
var num1 = 1.1
var num2 = 1.0
var num3 = 0.1 // 有效但是不推荐
```

由于保存 浮点数值 需要的 内存空间 是保存整数的两倍 ECMAscript 会在某些时候将浮点数转换为 整数。

```js
var floatNum1 = 1 // 直接转换为 1
var floatNum2 = 21.0 // 整数解析为 21
```

#### 使用科学技术法（用 e 表示），表示极大或极小的数字

> 数值（整数或者浮点数）e(或 E) 指数（10 次幂的指数） 【表示数值 与 10 次方乘积】

```js
3.2145e8 // 等于 321450000

45.12356e5 // 等于 4512356

1e-1 // 等于 0.1
3e-4 // 等于0.0003
```

#### 浮点数计算精度问题

由于采用 IEEE754 数据格式 的存储数字的原因, 浮点数计算的数值会产生舍入误差值的问题。【具体戳 [JS 0.1+0.2 的理解](https://www.jianshu.com/p/90ce596f131c)】javascript 浮点数计算精度最高 17 位小数，进行算数计算时候，精度远远不如整数。

[0.1 + 0.2 = 0.30000000000000004 怎样理解](https://www.cnblogs.com/shytong/p/5091600.html)

> 简单来说：计算机在计算时，都是先转换为 二进制后进行计算的，得到结果后，在转换为 10 进制。 刚好 0.1 和 0.2 转为二进制的时候，是个无限的位数，超出了 双精度位数限制（52 为）,进行了取舍，后进行的计算数值。

```js
0.1 + 0.2 === 0.3 // 输出false  0.1+0.2 的计算结果0.300000000000000004
// 永远不要测试某个特定的浮点数值
// 正确的比较方法是使用 JavaScript 提供的最小精度值：
console.log(Math.abs(0.1 + 0.2 - 0.3) <= Number.EPSILON)
//检查等式左右两边差的绝对值是否小于最小精度，才是正确的比较浮点数的方法
```

#### IEEE754【二进制浮点数算术标准】

IEEE754 标准包含一组实数的二进制表示法,它由符号位、指数位、尾数位三部分组成。

```
   符号位(s) * 尾数位(m) * 2^指数位(p)
```

![8biXQJ.png](https://s1.ax1x.com/2020/03/24/8biXQJ.png)

[![8bivLR.png](https://s1.ax1x.com/2020/03/24/8bivLR.png)](https://imgchr.com/i/8bivLR)

js 采用双精度存储（double precision），占用 64 bit。具体情况如：

```
    // IEEE 754 双精度 二进制 浮点表示法
    s    p指数位（11位）                m 尾数位 (共计52位，可表示53位)
    0     10000000001        010000000000000000000000000000000000000000000000000000

    // 根据以上数值进行计算

    (-1)^S*(1.M)*2^(E-1023)
    // M后面共计50个0  E= (10000000001)2 = (1025)10
    (-1)^0*(1.01000...)*2^(E-1023=2)

    // 注意此处 2进制乘法 相当于 进行小数点位移
    // 1 x 1.01 × 2∧2
    // 输出 101【输出的结果仍然是2进制，转为10进制是 5】


    // 指数 为啥 减1023？ 你没看图啊，那是偏移量。
    // 为啥指数位要减偏移量？ 因为指数也有正负,那么怎么表示？
    // 将真值映射到正数域数值（移码）。使用移码比较大小，只要高位对齐后，逐一比较就行
```

意义：

- 1 位用来表示符号位（s）
- 11 位用来表示指数(p)
- 52 位表示尾数(m)：IEEE754 规定，在计算机内部保存有效数字时，默认第一位总是 1，所以舍去，只保留后面的部分。比如保存 1.01，只存 01，等读取的时候再把第一位 1 加上去。所以 52 位有效数字实际可以存储 53 位。

> 根据 ECMAscript 规范： s 符号为 是 +1 或 -1，m 尾数位 是一个小于 2^53 但不小于 2^52 的正整数，p 指数位 是一个闭区间 -1074 到 971 中的整数。

**浮点数**

将该数字乘以 2，取出整数部分作为二进制表示的第 1 位；然后再将小数部分乘以 2，将得到的整数部分作为二进制表示的第 2 位；以此类推，直到小数部分为 0。

> 特殊情况： 小数部分出现循环，无法停止，则用有限的二进制位无法准确表示一个小数，这也是在编程语言中表示小数会出现误差的原因摘自[小数用二进制如何表示](https://blog.csdn.net/weixin_41042404/article/details/81276782)

```
    0.1
    >> parseInt(0.1*2), parseInt(0.2*2),parseInt(0.4*2),parseInt(0.8*2)......
    >> 0.0001 1001 1001 1001…（1001无限循环）

    0.2
    >>  parseInt(0.2*2), parseInt(0.4*2),parseInt(0.8*2),parseInt(0.6*2)......
    >> 0.0011 0011 0011 0011…（0011无限循环）
```

然而，js 采用的 双精度 尾数(m) 位数 是有限的（52 位），此时只能模仿十进制进行四舍五入了，但是二进制只有 0 和 1 两个，于是变为 0 舍 1 入。这即是计算机中部分浮点数运算时出现误差，丢失精度的根本原因。

**整数**

> 你以为精度丢失，只存在浮点数？
> 大于 9007199254740992 的**可能**会丢失精度。

由于 尾数位最大是 52 位，因此 JS 中能精准表示的最大整数是 Math.pow(2, 53)【安全值为 Math.pow(2, 53)-1】，十进制即 9007199254740992，所以大整数丢失精度的本质原因与浮点数一样。

```js
// 实际情况
9007199254740992 + 1 // 丢失
9007199254740992 + 2 // 未丢失
9007199254740992 + 3 // 丢失
9007199254740992 + 4 // 未丢失
```

推荐阅读 [从科学记数法到浮点数标准 IEEE 754](https://mp.weixin.qq.com/s/mf1mH-aGWgcC6v2R8ijE8A)、[IEEE-754 浮点转换器](https://www.h-schmidt.net/FloatConverter/IEEE754.html)、[JS Number 类型数字位数及 IEEE754 标准](https://blog.csdn.net/weixin_30832351/article/details/96015463?depth_1-utm_source=distribute.pc_relevant.none-task&utm_source=distribute.pc_relevant.none-task)

#### 数值范围

在计算机中，我们有一定的精度限制。

最大的数字是什么？

在数学中，你应该是无法给出具体数字。但是在 ECMAscript 中你可以。因为毕竟内存是有限的。而**JavaScript 中的 Number 类型 _基本_ 符合 IEEE 754-2008 规定的双精度浮点数规则**。

根据 ECMAscript 规范 `(s) * (m) * (2^e)` ，m 范围 【2^52 - 2^53 】 的正整数，p 指数位 是一个闭区间 -1074 到 971 中的整数

所以。。。

```js
// 能精确表示的整数范围上限,S为1个0，E为11个0，S为53个1
;((Math.pow(2, 53) - 1 ===
  Number.MAX_SAFE_INTEGER - // true
    // 能精确表示的整数范围下限,S为1个1，E为11个0，S为53个1
    (Math.pow(2, 53) - 1)) ===
  Number.MIN_SAFE_INTEGER(
    // true
    // 能表示的最大数字，S为1个0，E为971，S为53个1
    Math.pow(2, 53) - 1
  ) *
    Math.pow(2, 971)) ===
  Number.MAX_VALUE // true
// 能表示的最接近于0的正数，S为1个0，E为-1074，S为0
Math.pow(2, -1074) === Number.MIN_VALUE // true

// [MIN_SAFE_INTEGER, MAX_SAFE_INTEGER] 的整数都可以精确表示，
// 但是超出这个范围的整数就不一定能精确表示。这样就会产生所谓的大数精度丢失问题。
```

- **Number.MAX_VALUE**
  能表示的最大正数。最小的负数是 -MAX_VALUE。
- **Number.MIN_VALUE**
  能表示的最小正数即最接近 0 的正数 (实际上不会变成 0)。最大的负数是 -MIN_VALUE。
- **Number.NaN**
  特殊的“非数字”值。
- **Number.NEGATIVE_INFINITY**
  特殊的负无穷大值，在溢出时返回该值。
- **Number.POSITIVE_INFINITY**
  特殊的正无穷大值，在溢出时返回该值。

**ES6 新增**

- **Number.EPSILON**
  两个可表示(representable)数之间的最小间隔。[最小精度]
- **Number.MAX_SAFE_INTEGER**
  JavaScript 中最大的安全整数 (2^53 - 1)。
- **Number.MIN_SAFE_INTEGER**
  JavaScript 中最小的安全整数 (-(2^53 - 1)).

JavaScript 中的 Number 类型有 18437736874454810627(即 **2^64 - 2^53+3**) 个值。

ECMAscript 的 Number 类型基本符合 IEEE 754-2008 规定的双精度浮点数规则，但是 JavaScript 为了表达几个额外的语言场景（比如不让除以 0 出错，而引入了无穷大的概念），规定了几个例外情况：

- NaN，占用了 9007199254740990，这原本是符合 IEEE 规则的数字；
- Infinity，无穷大；
- -Infinity，负无穷

> 注意：javascript 的数值保存方式，可以保存正 0 和负 0，正 0 和负 0 被认为是相等的。

- 0/0 返回 NaN
- 正数/0、负数/-0 返回 Infinity
- 负数/0、正数/-0 返回 -Infinity

[解决 精度问题的方法](https://blog.csdn.net/weixin_34018202/article/details/88596223)

- 借助 Number.EPSILON 进行最小精度判断
- 先将小数处理为整数，再除以乘积 【有可能超出最大安全值】
- 通过字符串的方式，模拟计算，进行对应计算。【大部分成熟计算库都使用此方式】

##### 数值转换

**_number（）_**

1.  传入 Boolean true 是 1，false 是 0；
2.  传入 undefined 返回 NaN, null 返回 0,什么都不传 返回 0
3.  传入 字符串 纯数字 忽略前导 0，返回十进制数字。16 进制 Oxf1 等数字，转为 十进制数字。
4.  传入 字符串的包含 非数字 非空字符串 [字母符号等] 返回 NaN
5.  传入 对象： 先调用 valueOf()方法，如果返回 结果是 NaN ,继续调用 toString(),依据以上规则返回值

**parseInt（）**

1. 传入 空字符串、Boolean、undefined、null、对象等均返回 NaN
2. 传入 包含数字的字符串 比较复杂

   ```js
   parseInt(' 12 3 ') // 12
   parseInt('1dasd12') // 1

   // 会忽略 字符串 起始位置的空字符串、数字后面的字母、符号、空字符【之后的内容直接被截取】

   parseInt(' w1d') // NaN
   parseInt(' 1.12 ') // 1 其实取整就是 按照上面规则 一刀切
   parseInt('.12 ') // NaN
   // 起始 位置是字母、符号[+、- 正负除外]的 直接返回 NaN
   ```

3. 不会忽略前导 0，可能会被按照 八进制或十六进制 进行解析，单推荐传入第二个参数进行使用。
4. 进行 进制 转换，请传入第二参数，基数
   ```js
   parseInt('10', 2) // 2 按照二进制解析
   parseInt('10', 8) // 8 按照八进制解析
   parseInt('10', 10) // 10 按照十进制解析
   parseInt('10', 16) // 16 按照十六进制解析
   ```

**parseFloat（）**

1. 同 parseInt 传入 空字符串、Boolean、undefined、null、对象等均返回 NaN
2. 会忽略 字符串 起始位置的空字符串、数字后面的字母、符号、空字符【之后的内容直接被截取】
3. 传入 字符串 起始 位置是字母、符号[+、- 正负除外]的 直接返回 NaN
4. 传入整数，仍然会返回整数，传入小数仍然返回小数。
5. 忽略前导 0，只进行十进制 解析

**借助 运算符进行隐式转换**

1. 从 数字字符串 转换 成数字 使用 减法、乘法、除法[-、*、/]:

   ```js
   '12' - 0 // 12;
   'w12' - 0 // NaN;

   ' 12' * 1 // 12;
   '1/2' * 1 // NaN;

   '12' / 1 // 12;
   'w12' / 1 // NaN;
   ```

2. 从数字转为字符串 使用 加法 [ + ]

   ```js
   12 + '' // '12'
   12 + '0' // '120'
   ```

方法：

- **Number.isNaN() / isNaN()**
  确定传递的值是否是 NaN。默认对传入的 值进行转换后，进行判断`isNaN('123')===false;isNaN('blue')===true`;isNaN 同样适用于 对象。
- **Number.parseFloat()/parseFloat()**
  和全局对象 parseFloat() 一样。
- **Number.parseInt()/parseInt()**
  转换为整数
- **Number.isFinite() / isFinite()**
  确定传递的值类型及本身是否是有限数。
  **ES6 新增**
- **Number.isInteger()**
  确定传递的值类型是“number”，且是整数。
- **Number.isSafeInteger()**
  确定传递的值是否为安全整数 ( -(253 - 1) 至 253 - 1 之间)。

### Number 类

> 与 string 类 同样，也存在 Number 类。经过数据类型转换后，数值和数值对象可以被视为等价

通过 new 显示的生成数值对象`new Number()`

#### Number 类 的方法

> 建议在控制台中输入 new Number() 得到 Number 进行属性查看，同步理解

##### 函数或者构造函数

Number(value) : 将参数转换为字符串类型
new Number(value) : 得到一个 Sting 实例

##### Number.prototype 对象的 属性

| 字面量                        | 含义 ｜                                                                                              |
| ----------------------------- | ---------------------------------------------------------------------------------------------------- | ---------------------------------------------------------- |
| numObj.toExponential(digits)  | 以指数表示法（科学计数法）返回该数值字符串表示形式[digits：一个整数，用来指定小数点后有几位数字。]   | 123 得到 1.23e+2                                           |
| numObj.toFixed(digits)        | 方法使用定点表示法来格式化一个数值[digits：小数点后数字的个数；介于 0 到 20 （包括）之间，默认是 0 ] | ---                                                        |
| numObj.toLocaleString()       | 转换为和本地环境想对应的字符串                                                                       | ---                                                        |
| numObj.toPrecision(precision) | 转换为小数点形式（科学计数法）的字符串[precision:有效数字--除去小数点以外的位数]                     | 如，precision 是 6 123=>123.000;precision 是 2,123=>1.2e+2 |

### Undefined

Undefined 类型只有一个值，就是特殊的 undefined。

1. 在声明变了时，不进行初始化的值就是 undefined。

```js
var msg
// var name;
console.log(msg) // undefined  【只是声明了，未进行初始化负值】
console.log(name) // 报错无法执行 【没有进行变量声明而报错】

// 奇怪 的现象？？
console.log(typeof msg) // undefined
console.log(typeof name) // undefined
// 不管是为定义还是未声明 都是 无法真正操作的 所以，返回此种情况 是合理的
```

2. **undefined 可以进行赋值操作**【undefined、NaN 和 Infinity 都是全局对象 window 的属性。既然是属性，当然可以赋值。然而这三个属性又是不可写的属性，即它们的的内部特性[[writable]]为 false，所以赋值无效 】
3. undifined 不是常量不是保留字，你完全可以自定义一个叫做 undefined 的变量或者函数【 非全局作用域 】；比如 var undefined='foo'

```js
//此方法 表明是 undefined 是window 不可写的属性
Object.getOwnPropertyDescriptor(window, 'undefined')

// 2. undefined 全局模式可以赋值 不会报错，但是 赋值不会成功
undefined = 11
console.log(undefined, '全局')

// 3. undefined 可以 非全局作用域 进行赋值 使用
function foo() {
  var undefined = 10
  console.log(undefined)
}
foo() // 打印10
```

> 以上 摘自[由 ES 规范学 JavaScript(一)：为什么 undefined 可以被赋值，而 null 不可以？](https://segmentfault.com/a/1190000004212150)

4. **建议使用 void 0 代替 undefined** ?

```js
void 0 === undefined // true
/*
    JavaScript 的代码 undefined 是一个变量，而并非是一个关键字，
    这是 JavaScript 语言公认的设计失误之一，
    所以，我们为了避免无意中被篡改，
    建议使用 void 0 来获取 undefined 值 -- 《重学前端-程劭非（winter）》
    */
```

> [void 运算符 对给定的表达式进行求值，然后返回 undefined](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/void)

5. if 判断 通过`msg===undefined` 或 `typeof(msg)==="undefined"` 的形式确定变量是否是 undefined；【undefined 是 false !undefined 是 true】
6. 一个函数如果没有使用 return 指定返回值，将会返回一个 undefined 值

### Null 类型

Null 类型 只有一个值 null。null 是缺少标识符，表示变量 未指向任何对象【 这就是 typeof null 返回“object” 的原因 】

1. 如果定义变量将来要用来保存对象，初始变量最好是 null。
2. undefined 实际派生自 null
3. null 是一个字面量[与 true 和 false 类似]。属于 JavaScript 的保留字,不可以赋值
4. `null==undefined` ; `null!==undefined`、

### Boolean 类型

Boolean 类型 有两个 字面量，true 和 false。

> 字面量 true 和 false 区分大小写，与 True 和 False(以及其他形式的大小写)都不是 Boolean，知识标识符。

#### 其他类型转换为 Boolean

1. 使用`Boolean()`方法
2. 使用`!!msg`
3. if 条件语句中 自动转换

```js
var msg = 'hello'

console.log(Boolean(msg), 'Boolean(msg),!!msg', !!msg) // true "Boolean(msg),!!msg" true
```

**各类型不同值对应转换规则**

| 数据类型  | 转换为 true 的值 | 转换为 false 的值 |
| --------- | ---------------- | ----------------- |
| Boolean   | true             | false             |
| String    | 任何非空字符串   | 空字符串（''）    |
| Number    | 非 0 数字        | 0 和 NaN          |
| Object    | 任何对象         | null              |
| Undefined | --不存在--       | undefined         |
