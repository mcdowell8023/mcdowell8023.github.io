---
layout: pt
title: sass学习笔记
date: 2018-10-25 20:20:28
categories:
  - web前端笔记
tags:
  - sass
comments: false
---

## sass 介绍
##### 特性
- 在 CSS 基础上增加**变量**、**嵌套 (nesting)**、**混合 (mixins)** **导入 (inline imports)** 等功能
- 通过函数进行**颜色值与属性值的运算**
- 提供 **控制指令 (control directives)** 等高级功能
- **自定义输出格式**

##### 语法
分scss 和 sass 两种：
- scss ：仅在 CSS3 语法的基础上进行拓展，所有 CSS3 语法在 SCSS 中都是通用的【说白了就是带花括号那种，与css语法基本一致】
- sass ： 传统sass（缩进格式 (Indented Sass) ）使用 “缩进” 代替 “花括号” 表示属性属于某个选择器，用 “换行” 代替 “分号” 分隔属性【没有｛｝和; 那种】

> 任何一种格式可以直接 导入 (@import) 到另一种格式中使用，或者通过 sass-convert 命令行工具转换

## CSS 功能拓展

#### 嵌套规则

```
#main {// 父级
  width: 97%;

  p, div { // 子级
    font-size: 2em;
    a { font-weight: bold; }
  }

  pre { font-size: 3em; } // 子级
}
// build after
#main {width: 97%; }
#main p, #main div {font-size: 2em;}
#main p a, #main div a {font-weight: bold;}
#main pre {font-size: 3em; }
```
> 嵌套功能避免了重复输入父选择器 （有说不要超过三层，否则会造成）

#### 父选择器 &

```
#main {
  color: black;
  a {
    font-weight: bold;
    &:hover { color: red; }
  }
}
// build after
#main { color: black; }
#main a { font-weight: bold; }
#main a:hover { color: red; }

```
> **&** 会换成 父选择器（指向父级）；多层嵌套，最外层的父选择器会一层一层向下传递

```
#main {
  color: black;
  &-sidebar { border: 1px solid; }
}
// build after
#main { color: black; }
#main-sidebar {border: 1px solid; }
```

> & 必须作为选择器的第一个字符，其后可以跟随后缀 **生成复合的选择器**

#### 属性嵌套
sass可以 将属性嵌套在命名空间中,避免重复输入及方便管理
```
.funky {
  font: { // 可以包含自己的属性 如 font: 20px/24px {
    family: fantasy;
    weight: bold;
  }
}
// build after
.funky {font: 20px/24px;font-family: fantasy;font-weight: bold; }
```

#### 占位符选择器 %foo

常用的 id 与 class 选择器写法相似，只是 # 或 . 替换成了 %。必须通过 @extend 指令调用【具体了解，详看@extend-Only 选择器】

## 注释

##### 多行注释 /* */
> 被完整 编译到 CSS 文件
##### 以及单行注释 //
> 不会编译到css 文件

## SassScript
SassScript 可作用于任何属性，允许属性使用变量、算数运算等额外功能

#### Interactive Shell
 可以在命令行中测试 SassScript 的功能。在命令行中输入 sass -i

```
$ sass -i
>> "Hello, Sassy World!"
"Hello, Sassy World!"
>> 1px + 1px + 1px
3px
>> #777 + #777
#eeeeee
>> #777 + #888
white
```
#### 变量 $
变量支持块级作用域

```
// 变量的声明
$width: 5em;
$blue: #f29e;
// 变量的使用
#main {
  $height:30px;
  $red : red !global; // 在局部声明了全局变量

  width: $width;
  height:$height;
  color: $blue;
}
div{
    height:$height; // 错误，局部变量只能在局部使用

    color: $red;
}

```
> 嵌套规则内定义的变量只能在嵌套规则内使用（局部变量），

> 不在嵌套规则内定义的变量则可在任何地方使用（全局变量）。


> 将局部变量转换为全局变量可以添加 **!global**

#### 数据类型 （Data Types）

- 数字： 1,2,3,10px
- 字符串： "foo",'bar',baz
- 颜色：blue, #29ef, rgba(255,0,0,0.5)
- 布尔值：true,false
- 数组（list）,用空格或逗号分割：1.5rem 1em 0 2em / Helvetica, Arial, sans-serif
- maps 相当于javascript的object：（key1:value1,key2:value2）

> SassScript 支持其他css属性，比如unicode字符集，或者！important（作为无引号字符串【不进行处理】）

##### 字符串（String）

#{}的使用
```
@mixin firefox-message($selector) {
  body.firefox #{$selector}:before {
    content: "Hi, Firefox users!";
  }
}
@include firefox-message(".header");

// build to

body.firefox .header:before {
  content: "Hi, Firefox users!"; }
```
> #{} (interpolation) 时，有引号字符串将被编译为无引号字符串，这样便于在 mixin 中引用选择器名

##### 数组 (Lists)
数组常用于margin: 10px 15px 0 0 或者 font-face: Helvetica, Arial, sans-serif 这样**通过空格或者逗号分隔的一系列的值** （独立的值也算作数组）

**数组方法：**
1. nth 直接访问数组中的某一项 （类似于jQ的eq）；
2. join 数组拼接；
3. append 数组中添加新值；
4. @each 遍历数组中的每一项

**二维数组写法：**

1px 2px, 5px 6px 是包含 1px 2px 与 5px 6px 两个数组，用','分隔

(1px 2px) (5px 6px) 是包含 1px 2px 与 5px 6px 两个数组，用' '（空格）分隔

（） 表示不包含任何值的空数组或map（在 Sass 3.3 版之后）

##### Maps
Lists一样Maps主要为sassscript函数服务

1. map-get函数用于查找键值
2. map-merge函数用于map和新加的键值融合
3. @each命令可添加样式到一个map中的每个键值对。

> Maps可用于任何Lists可用的地方，在List函数中 Map会被自动转换为List ，
如 (key1: value1, key2: value2)会被List函数转换为 key1 value1, key2 value2 ，反之则不能。

#### 运算

所有数据类型均支持相等运算 == 或 !=

##### 数字运算 (Number Operations)
> 加减乘除、取整等运算 (+, -, *, /, %)【会在不同单位间转换值】

> 关系运算 <, >, <=, >= 也可用于数字运算

```
p {
  width: 1in + 8pt;
}

//build to

p { width: 1.111in; }
```
**除法（/）**
1. 如果值，或值的一部分，是变量或者函数的返回值 font: 10px/8px;
2. 如果值被圆括号包裹 height: (500px/2);
3. 如果值是算数表达式的一部分 margin-left: 5px + 8px/2px;

### 持续更新中....