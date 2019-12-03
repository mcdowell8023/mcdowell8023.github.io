---
title: vue项目调整兼容IE8+
comments: true
mathjax: true
categories:
  - 技术帖
tags:
  - 兼容IE
  - vue
date: 2019-11-28 14:29:00
---

# 记一次，vue项目调整兼容IE8+

## 要点

- flex 样式兼容
- element UI 部分组件兼容
- 

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


##### IE下 table 组件 下表格与表头错位的兼容问题

##### 问题

因为表格宽度未设置100%，导致计算每列出现几px的精度差，最后导致整体样式错乱

##### 解决

全局接入 css补丁:

```bash
  body .el-table th.gutter {
    display: table-cell !important;
  }

  body .el-table colgroup.gutter {
    display: table-cell !important;
  }

  table {
    width: 100% !important;
  }
```

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


### IE9、10、11、edge 下 头像裁剪上传 报错（提示对象或方法未定义）

由于此处代码是同事写的，屡了下流程：

  1. 通过 点击 label标签 触发 隐藏 input file控件，选取图片;
  2. 通过 FileReader 读取文件 使用 FileReader.readAsArrayBuffer() 得到 blob对象;
  3. 使用 window.URL.createObjectURL 得到 dataUrl 喂给 [vue-cropper](https://github.com/xyxiao001/vue-cropper) 组件进行图片裁剪;
  4. 从 vue-cropper 裁剪后，返回得到 base64，通过 new File() 转换为file 文件; // 或者 blob对象
  5. 使用formData 发送给后台;
  6. 后台返回成功后，修改页面头像，流程完毕。


了解到都使用哪些东西，还需要了解下基本知识。
MDN 下了 [File](https://developer.mozilla.org/zh-CN/docs/Web/API/File)、 [blob](https://developer.mozilla.org/zh-CN/docs/Web/API/Blob)、[URL.createObjectURL](https://developer.mozilla.org/zh-CN/docs/Web/API/URL/createObjectURL)
技术实现采用了一些比较高级的api ，对于IE来讲不是太友好，总结如下：

浏览器| File | blob | createObjectURL | new File(转换为File文件)
---   |  --- | ---  |     ---         | ----
IE9   | NO   | NO   |       NO        | NO
IE10  | YES  | YES  |       YES       | NO
IE11  | YES  | YES  |       YES       | NO
Edge  | YES  | YES  |       YES       | NO


看起来情况不太乐观。那么，一个一个解决。

#### IE9 下头像上传

从上面看来，IE9不支持高级api ，原来的整套应该全部不适用。

果然，从 第一步 获取文件就在报错。 input file控件 的 target.files 是 udefined。难道无法拿到文件？

补习了下：

- [IE9 获取File文件对象](https://my.oschina.net/tianshl/blog/860163)
- [前端图片预览，上传前预览，兼容IE7、8、9、10、11，Firefox，Chrome](https://www.cnblogs.com/rubylouvre/p/4597344.html)

得到如下信息：

> 低版本IE由于JS安全问题，不允许JS访问本地文件,所以无法获取files
> 对于低版本的IE可以使用ActiveXObject获取文件对象, 但是默认情况下
> ActiveXObject为不可用的, 所以要想使用此对象要先启用设置, 即:
> Tools(工具) / Internet options(选项) / Security(安全) / Custom level(自定义级别)
> 找到"Initialize and script ActiveX controls not marked as safe for scripting"
> 设置为"Enable(not secure)"即可.


> **获取文件路径**
> 出于安全性的考虑，低版本IE上传文件时屏蔽了真实的本地文件路径,
> 以C:\fakepath\**取而代之, 所以默认情况下通过fileEle.value 不能获取到
> 文件的真实路径.
> 如果想获取真实路径, 有两种方式:
> 1. 通过设置IE的安全设置, 即:
>     Tools(工具) / Internet options(选项) / Security(安全) / Custom level(自定义级别)
>     找到"Include local directory path when uploading files to a server"
>     设置为的"Enable"
> 2. 使用JS获取, 即:
>     fileEle.select().blur();
>     var filePath = document.selection.createRange().text;

然后试了下 在IE 下使用 ActiveXObject 读取文件。然后读取文件还要设置【⊙﹏⊙b汗】，放弃。


与产品沟通了下。本着华丽升级，优雅降级的原则【好吧，就是我也没有办法】。IE9下可以取消裁剪。



先科普下


[兼容ie9文件上传，解决ie9下提示下载或保存](https://www.cnblogs.com/ylxmt/p/10132621.html)

[HTML5 file对象和blob对象的互相转换](https://www.jianshu.com/p/5b44c41adfe2)

[js blob转File](https://blog.csdn.net/zhoudekuai/article/details/88360076)

[Convert blob to file](https://stackoverflow.com/questions/27553617/convert-blob-to-file)





[HTML利用meta标签设置IE文档模式及相关知识拓展](https://www.w3h5.com/post/122.html)