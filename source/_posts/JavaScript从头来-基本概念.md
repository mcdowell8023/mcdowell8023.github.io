---
title: JavaScript从头来-基本概念
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

### 区分大小写

变量、函数名、操作符都区分大小写

### 标识符

标识符指变量、函数、属性的名字、或者函数的参数。

组合规则：

1. 第一个字符必须是字母、下划线（\_）、或者美元( \$ )
2. 其他字母可以是字母、下划线、美元或者数字
3. 驼峰写法： 第一个字母小写 剩下的每个单词首字母大写

### 严格模式

头部添加`' use strict '`，表示在严格模式下的执行。

> 严格执行模式下，执行结果会有很大不同。支持浏览器：IE10+、Firefox4+、Safari5.1+、Opera12+、Chrome

---

### 语句

1. 每条语句，分号结尾（;）【 现代项目架构中，不加分号是编译后，统一由脚手架添加 】。
2. 使用花括号 （{}）进行代码块分割，明确意图，降低出错情况。

### 关键字和保留字

就有特殊用途的--关键字，如：

    void break  return do case else new var ...

预留的不能用作特殊字符的保留字，如：

    boolean float native init short ...

### 变量

ECMAscript 变量是非常松散的（可以保存任何**_类型_**的数据）。每个变量仅仅只是一个用于存值的占位符。

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

> 关于 变量、常量、字面两：
> 变量、常量是引用，变量值可变，常量值固定。字面量是值
