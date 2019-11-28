---
title: vue项目调整兼容IE9
comments: true
mathjax: true
categories:
  - 技术帖
tags:
  - 兼容IE9
  - vue
date: 2019-11-28 14:29:00
---

# 记一次，vue项目调整兼容IE9

## 要点

- 111

## 详细

### 交代说明

公司项目开发接近收尾,突然硬性要求兼容ie9。项目搭建时，采用vue2.6 + elementUI + axios,使用大量es6语法（Promise），和flex 布局。

### 需要面临的问题

- elementUI 针对 IE 部分存在兼容问题
- axios 针对IE 下返回表现不同
- flex 只支持到IE10
- Promise IE系（不含除去 Edge） 不支持

---

### 基本兼容垫片

[babel-polyfill](https://babeljs.io/docs/en/babel-polyfill/)

```bash
  npm install --save-dev babel-polyfill
```

在webpack config.js中加入:

```js
  module.exports = {
    entry: {app: ['babel-polyfill', './src/main.js']
  }
```

---

### IE 11 中接口返回值接收失败

查找发现Axios 返回值 response 的 data 是个字符串的json。需要转换为JavaScript对象，才可正常读取。
代码如下：

```js
import axios from 'axios'
import config from './config'
const Axios = axios.create(config);
// 用于转换为 JavaScript对象
const looseJsonParse = function(obj){
  return Function('"use strict";return (' + obj + ')')()
};
// Response 拦截器
Axios.interceptors.response.use(
  response => {
    var pathsearch = window.location.pathname + window.location.search
    if (response) {
      var res = typeof response.data === 'string' ? looseJsonParse(response.data):response.data;
      switch (res.status) {
        case '200':
          return res
        case '40001':
          console.log(response)
          return 0
        default:
          return res
      }
    }
  },
  error => {
    return error
  }
)
export default Axios
```

> 关于 looseJsonParse 转换为 JavaScript对象
> 其实也可使用 JSON.parse() 【 ie8+ 】
> 虽然可以 eval('(' + obj + ')')，但不建议使用
> 由于性能和容易被劫持攻击等原因，推荐使用 looseJsonParse 方式 [戳这里](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/eval)

#### 插曲：eval 与 JSON.parse()

详细了解？[戳这里](https://www.cnblogs.com/lovesong/p/6036650.html)

- **JSON.parse** 只是格式化json;要求严格的json格式（只能解析属性名是双引号包裹的字符串对象【key、value 都要双引号】，并会忽略换行和空格（值外面））。
- **eval** 函数可将一个JavaScript代码字符串求值成特定的对象，解析json 只是其中一点功能。
  
  1. eval 解析 json 需要加入'()' eg: eval('(' + obj + ')')
  2. eval 对josn 的格式没有特殊要求，但是会直接执行里面内容进行运算。（eval 相当于一个执行环境）eg:  eval('(' + '{data:new Date()}' + ')')
   
  > eval 解析 json 需要加入'( )' 是因为
  > eval()相当于一个执行环境，当你不加括号的时候，jsonstr1会被认为是一条复合语句。运行的时候就会逐个字符的解析。
  > 但是加上括号的时候，jsonstr1就当做一个表达式去运算。从括号开始就被当做了对象进行识别。

---

### IE 9 中接口返回值的 data 为undefined

#### 查找发现

在Axios config 中，默认设置了 responseType: 'json' 。 ie9下不支持,返回数据没有data 字段

#### 解决

注释 // responseType: 'json' 就好

---

### IE中 elementUI 组件 bug

#### NavMenu 导航 组件 移入下拉报错（TypeError 对象不支持此操作）

##### 查找发现

NavMenu组件默认使用的hover 方式不被IE 支持

##### 解决

在对应的 el-menu 加入属性 menu-trigger="click" mode="horizontal"

> 注意：
> 文档说的很明确，menu-trigger (只在 mode 为 horizontal 时有效)

---

### IE 下报错 ：Unhandled promise rejection SyntaxError: 缺少 ';' 或 Unhandled promise rejection SyntaxError: 缺少 '）'

##### 查找发现

vue-chart出现了兼容问题。

##### 解决

[查阅文档](https://github.com/ecomfe/vue-echarts/blob/master/README.zh_CN.md)

- 步骤1： npm i echarts vue-echarts -S
- 步骤2：npm i resize-detector -S

- 步骤3：
  当使用 Vue CLI 3+ 时，需要在 vue.config.js 中的 transpileDependencies 增加 vue-echarts 及 resize-detector，如下：

  ```bash
    // vue.config.js
    module.exports = {
      transpileDependencies: [
        'vue-echarts',
        'resize-detector'
      ]
    }
  ```

  当使用 Vue CLI 2 的 webpack 模板时，需要按下述的方式修改 build/webpack.base.conf.js：

  ```bash
  {
            test: /\.js$/,
            loader: 'babel-loader',
    -       include: [resolve('src'), resolve('test')]
    +       include: [
    +         resolve('src'),
    +         resolve('test'),
    +         resolve('node_modules/vue-echarts'),
    +         resolve('node_modules/resize-detector')
    +       ]
  }
  ```


- 还有一种方案，直接使用echarts

---

### 兼容样式问题

##### 问题

最早使用了大量的flex 布局，导致IE9无法正常显示。

##### 解决

在index.html 中借助 “<!--[if IE 9]>” 来引入针对IE9 的样式补丁，如下：

```html
  <!--[if IE 9]>
    <style>
      .flex-row-between>:nth-child(1){
        float: left;
      }
      .flex-row-between>:nth-child(2){
        float: right;
      }
      img{
        vertical-align:middle;
      }
    </style>
  <![endif]-->
  <!-- 或者，也比较建议借助link 引入补丁样式 -->
  <!--[if IE 9]>
    <link rel="stylesheet" href="static/compatible/forIE9.css">
  <![endif]-->
```
