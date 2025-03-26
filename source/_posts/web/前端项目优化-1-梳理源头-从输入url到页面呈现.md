---
title: 前端项目优化-1. 梳理源头-输入url到页面呈现
comments: true
mathjax: true
categories:
  - 技术帖
tags:
  - web
  - 优化
  - 追本溯源
date: 2024-03-04 22:22:22
---

## 从更高维度审视页面

作为一名合格的开发者, **要能站在用户体验角度来考虑页面性能**。我们看下面几个常见的用户体验指标:

* 页面加载时长:
  * 当用户请求一个网站时，如果在 **1 秒内看不到关键内容**，用户会产生任务被中断的感觉。
* 用户交互反馈时长:
  * 当用户点击某些按钮时，如果 **100ms 内无法响应**，用户会感受到延迟。
* Web 动画中的帧数:
  * 如果 Web 中的动画**没有达到 60fps**，用户会感受到动画的卡顿。

通常，这些指标是由一系列的复杂因素导致的。如果你要开发流畅的页面，或者诊断 Web 页面中的性能问题，那你就需要了解 URL 是怎么变成页面的，以及在这个过程中都发生了什么。

比如， 首屏的显示就涉及了 DNS、HTTP、DOM 解析、CSS 阻塞、JavaScript 阻塞等技术因素，其中一项没处理好就可能导致整个页面的延时。

## Chrome 多进程架构

首先，在开始讲解整个过程前，我们需要认识一下 Chrome 多进程架构。因为，从浏览器输入 URL 到页面渲染的整个过程都是由 Chrome 架构中的各个进程之间的配合完成。

Chrome 的多进程架构：

* **浏览器主进程**，它负责用户界面（地址栏、菜单等等）、子进程的管理（例如，进程间通信和数据传递）、存储等等
* **渲染进程**，它负责将接收到的 HTML 文档和 JavaScript 等转化为用户界面
* **网络进程**，它负责网络资源的请求，例如 HTTP请求、WebSocket 模块
* **GPU（图形处理器）进程**，它负责对 UI 界面的展示
* **插件进程**，它负责对插件的管理

## 输入 URL 到页面呈现的过程

简单来说，整个过程可以分为以下几个步骤：

1. **DNS 解析**:
   * 用户输入 URL 后，浏览器会先进行 DNS 解析，将域名解析为 IP 地址。
2. **建立 TCP 连接**:
    * 浏览器通过 IP 地址找到服务器后，会建立 TCP 连接。
3. **发送 HTTP 请求**:
    * 浏览器向服务器发送 HTTP 请求。
4. **服务器处理请求**:
    * 服务器接收到请求后，会处理请求，返回相应的数据。
5. **浏览器接收响应**:
    * 浏览器接收到响应后，会解析响应数据，生成 DOM 树。
6. **浏览器渲染页面**:
   * 浏览器解析 CSS，生成 CSSOM 树。
    * 将 DOM 树和 CSSOM 树结合成 Render 树。
    * 浏览器根据 Render 树进行页面布局和绘制。
    * 用户可以看到页面上的内容。
7. **关闭 TCP 连接**:
    * 当浏览器不再需要服务器返回的数据时，会关闭 TCP 连接。
8. **页面加载完成**:
    * 页面加载完成后，用户可以看到页面上的内容。
    * 用户可以与页面上的内容进行交互。
        * 用户可以点击页面上的链接，转到其他页面。
        * 用户可以输入表单数据，提交给服务器。
        * 用户可以执行页面上的 JavaScript 代码。

### 解析输入

发生这个过程的前提，用户**在地址栏中输入了 URL**，而地址栏会根据用户输入，做出如下判断：

* **非URL结构的字符串**，则会用浏览器默认的搜索引擎**搜索该字符串**
* **URL 结构字符串**，则会 **解析URL结构**，浏览器进程会将完整的 URL 通过IPC，**发送给网络进程**

### 通信过程

#### HTTP请求流程

##### 构建请求

首先会构建请求行，它包括：**请求方法、请求url、协议版本**

```bash
  GET /index.html HTTP1.1
```

##### 查找HTTP缓存

根据上次请求的结果，**查找缓存**，分别依据强缓存 `cache-control` / `expires` 和协商缓存 `last-modified` / `Etag` 进行判断。 如果命中缓存，会直接返回缓存数据，并直接结束请求。

* 缓解服务器端压力，提升性能（获取资源的耗时更短了）；
* 对于网站来说，缓存是实现快速资源加载的重要组成部分。

具体看 (网络协议-HTTP核心问题## HTTP 缓存)[]

##### 准备IP 和 端口

##### DNS 解析得到IP

首先，**进行 DNS 解析域名得到对应的 IP**。 DNS 解析会 经过 递归查询 客户端、系统 缓存 在到 本地 DNS 服务器，如果没有再到 遍历查询 根域名服务器、顶级域名服务器、权威 DNS 服务器。最终得到 IP 地址。

具体看 (网络协议-DNS)[]

##### 等待 TCP 队列

1. 浏览器对 TCP 连接数的限制, 超过 `6个 TCP` 链接 会进入等待队列

* 现代浏览器 为了**优化性能**和 **避免过多的并发连接对服务器造成压力**，会 限制对 **单个域名限制 6 个并发 TCP 连接**。

2. TCP 连接复用的限制

* HTTP/1.0 中，**每个请求都需要建立一个新的 TCP 连接**，连接完成后立即关闭（每次都要三次握手，四次挥手）
* HTTP/1.1 中，尽管支持持久连接（Keep-Alive），允许复用 TCP 连接，但 **每个 TCP 连接一次只能处理一个请求**
* HTTP/2 中，虽然 **支持多路复用**（一个 TCP 连接可以同时处理多个请求），但如果 **某个请求被阻塞，其他请求可能会被间接阻塞** (TCP 阻塞机制 )
* HTTP/3：基于 QUIC 协议（UDP），彻底解决了 TCP 的队头阻塞问题: 即使某个流（请求）被阻塞，其他流仍然可以继续传输，不会影响整个连接

> 如果在同一个域名下同时有 10 个请求发生，那么其中 4 个请求会进入排队等待状态，直至进行中的请求完成。

##### 建立 TCP 连接

不用等待 TCP 队列后, 拿到 IP 后， 首先，建立 TCP 连接，即三次握手过程：

1. 客户端发送标有 `SYN = 1，seq = x` 的数据包，表示 将要发送请求(x 为随机数)。
2. 服务器发送标有 `SYN = 1，ACK=1, ack = x + 1，seq = y` 的数据包，表示 **已经收到通知，告知客户端发送请求**(y 为随机数)。
3. 客户端发送标有 `ACK = 1, ack = y + 1，seq = x + 1` 的数据包，表示 **确认收到服务器的响应**。

##### 进行http 通信

然后，利用 TCP 通道进行数据传输：

###### 发起 HTTP 请求

请求行
请求头
请求体（只有post请求有，get请求没有）

###### 接收 服务器处理响应

响应行：版本协议、状态码
响应头
响应体

1. 发包收包，不断重复这个过程
2. TCP 的重发机制：客户端在**发送一个数据包后，未接收到服务端的确定消息**，则**重新发送该数据包**
3. 当接收完所有的数据包后，接收端会按照 TCP 头中的需要进行排序，形成完整的数据

##### 关闭 TCP 连接

最后，断开 TCP 连接，即四次握手过程：

终止一个连接要经过 4次握手。
因为 `CP 连接是 **全双工** (即数据在两个方向上能同时传递，同时双向传输数据)， 因此**每个方向必须单独地进行关闭**` 。
这原则就是 当一方完成它的数据发送任务后，就能发送一个 FIN 来终止这个方向连接。当一端收到一个 FIN，它必须通知 应用层 另一端已经 终止了数据传送。

四次挥手过程如下：

1. 客户端发送一个 FIN，用来关闭客户端到服务器的数据传送。
2. 服务器收到这个 FIN，发送一个 ACK 给客户端，确认序号为收到的seq number + 1。
3. 服务器完成发送数据，向客户端发送一个 FIN。
4. 客户端发送一个 ACK 给服务器，确认序号为收到的seq number + 1。

### 数据处理

当网络进程接收到的响应报文状态码，进行相应的操作。

状态码为 200 OK 时，会解析响应报文中的 Content-Type 首部字段 解析 MIME 类型。
Content-Type 会出现 application/javascript、text/css、text/html，即对应 Javascript 文件、CSS 文件、HTML 文件。

### 创建渲染进程

浏览器网络线程 进行 网络通信**获取HTML代码**，然后进入**渲染主线程**的**消息队列**进行包装，得到渲染任务后，则需要创建渲染进程，用于后期渲染。
而对于渲染进程，如果是**同一站点是可以共享一个渲染进程**，例如 a.abc.com 和 c.abc.com 可以共享一个渲染渲染进程。
否则，需要重新创建渲染进程

![渲染开始时间点](/images/web_optimize/1/render_start.png)

> 需要注意的是，**同站指的是顶级域名和二级域名相等**

### 开始渲染

在创建完渲染进程后，网络进程会将接收到的 HTML、JavaScript 等数据传递给渲染进程。而在渲染进程接收完数据后，此时用户界面上会发生这几件事：

* 更新地址栏的安全状态
* 更新地址栏的 URL
* 前进后退此时 enable，显示正在加载状态
* 更新网页

### 渲染过程

大家都知道页面渲染的过程也是面试中单独会考的点，并且时常会由这个点延申出另一个问题，即如何避免回流和重绘。

![总流程](/images/web_optimize/1/render_1.png)

渲染过程，是整个从理器输入 URL 到页面渲染过程的最后一步。而页面渲染的过程可以分为 9 个步骤：

解析html-Parse HTML 生成 DOM 树
解析 CSS  生成 CSSOM
加载或执行 JavaScript
生成渲染树（Render Tree）
布局
分层-Layer
生成绘制-Paint列表
分块-Tiling
光栅化-Raster
画-Draw（合成）

#### 构建DOM树 && 解析CSS生成CSSOM

 该步骤主要生成DOM树和CSSOM树。html代码解析后生成DOM树，css代码解析后生成CSSOM树。

![构建 DOM 树](/images/web_optimize/1/render_Parse_HTML.png)

> 注意的是这个 DOM 树不同于 Chrome-devtool 中 Element 选项卡的 DOM 树，它是存在内存中的，用于提供 JavaScript 对 DOM 的操作。

在解析过程中，为了提⾼解析效率，浏览器会启动⼀个预解析器率先下载和解析 CSS。

![解析 CSS  生成 CSSOM](/images/web_optimize/1/render_Parse_HTML1_1.png)

> CSS Object Model 是一组允许用 JavaScript 操纵 CSS 的 API。详细 API 讲解可以看 MDN

#### 加载 JavaScript

通常情况下，在构建 DOM 树或 CSSOM 的同时，如果也要加载 JavaScript，会造成前者的构建的暂停。

因为JS可能会改变DOM树， 所以需要先解析，但如果声明是异步加载，那就不会暂停。当然 **预解析线程**可以分担⼀点**下载 JS 的任务**。

![渲染开始时间点](/images/web_optimize/1/render_Parse_HTML1_2.png)

> ⚠️ 可以通过 defer 或 sync 来实现异步加载 JavaScript。
> sync  ： 虽然 **可以实现异步加载 JavaScript，但是 仍然会造成阻塞**
> defer ： 则是 **等待 CSSOM 和 DOM 树构建完后才执行 JavaScript**

##### 编译和运行 JavaScript 的过程

由于 JavaScript 是解释型的语言。所以这个过程会是这样的：

* 针对每句代码进行分行处理，即 Token 化
* 根据 Token，生成 AST（Abstract Sytanx Tree） 抽象语法树和创建上下文
* 解释器解析和执行 AST，生成字节码。
* 编译器针对需要反复执行的代码，生成对应的机器码，提高运行效率

#### 生成渲染树（Render Tree）

在有了 DOM 树和 CSSOM 之后，需要将两者结合生成渲染树 Render Tree，并且这个过程会去除掉那些 display: node 的节点。此时，渲染树就具备元素和元素的样式信息。

![解析 CSS  生成 CSSOM](/images/web_optimize/1/render_Recalculate_Style.png)

#### 布局（第二次称回流 或者重排）

根据 Render Tree 渲染树，对树中每个节点进行计算，确定每个节点在页面中的宽度、高度和位置。

![布局](/images/web_optimize/1/render_layout.png)

##### 布局树和可见内容是一一对应的，与 DOM树并不一定一一对应

1、**display值为none的元素**，只是不可见，**DOM树 添加**，**布局树 不添加**。

![渲染开始时间点](/images/web_optimize/1/render_layout_1.png)

2、**before伪类添加的元素**，由于不算做一个单独的元素，**DOM树 不添加**，**布局树添加**。

![渲染开始时间点](/images/web_optimize/1/render_layout_2.png)

3、浏览器在渲染过程中为了保持布局的连贯性和符合CSS规范会自动创建一些布局盒子：**匿名行盒与匿名块盒**，**DOM树 不添加**，**布局树添加**。

![渲染开始时间点](/images/web_optimize/1/render_layout_3.png)

> 需要注意的是，第一次确定节点的大小和位置的过程称为布局，而第二次才被称为**回流**

#### 分层

在现代浏览器中，为了**优化渲染性能**，渲染树被**分解成多个层**（Layers）。**每个层可以独立于其他层进行渲染和合成**，这有助于减少绘制和合成的工作量。

![渲染开始时间点](/images/web_optimize/1/render_layer.png)

  跟堆叠上下文有关的属性，会影响分层，比如 `z-index` ， `opacity` 、 `transform` 、 `filter` ，或者元素被设置为 `will-change` ，以及部分 3D 的效果、动画 和过度效果可能也会被分为新的层。[内容溢出存在滚轮的情况也会进行分层]

[告知浏览器提前优化 will-change](https://developer.mozilla.org/zh-CN/docs/Web/CSS/will-change)

> 相关拓展
> opacity属性 与 堆叠上下文有关，因为 **当元素的opacity值小于1时，它将创建一个内部的堆叠上下文**。
>
> * 这意味着，即使元素的z-index值较低，它的不透明部分仍然可以覆盖在其后面的元素的不透明部分。
> * 这可能导致一些不可预见的层叠效果，因为**元素的不透明度会影响其在堆叠上下文中的行为**。
>
> 例如，假设有两个元素A和B，A在B的上方，A的z-index值较高，但A的opacity为0.5，B的opacity为1。在这种情况下，A的不透明部分将覆盖B，但A的半透明部分将允许B的内容显示出来。这是因为A的半透明部分创建了一个内部堆叠上下文，而B的内容在这个内部堆叠上下文中显示。

##### 堆叠上下文的层级规则如下

* 根元素（通常是HTML元素）形成一个堆叠上下文。
* 定位元素（position属性为relative、absolute或fixed）可以形成新的堆叠上下文。
* 元素的z-index属性可以指定其在堆叠上下文中的层级。
* 某些CSS属性和值，如opacity、transform、filter等，可以创建新的堆叠上下文。

#### 绘制-Paint (重绘在这里)

为分层结果的**每⼀层**⽣成如何 **绘制的指令**，并不是真的绘制。
到这里**渲染主线程**的工作 就 结束了，剩下的任务交给其他线程完成。

#### 分块-Tiling

有了绘制列表后，渲染引擎中的**合成线程**会 根据 当前视口的大小 将图层进行**分块处理**。
这一步会**将每⼀层**分为**多个小的区域**。

分块的目的是对分层结果进行进一步细分，通过**只渲染用户可以看到的部分**（即视口中的部分），浏览器可以更快地完成绘制工作。
当用户**滚动页面时**，浏览器可以**丢弃不在视口中的块**，并重新绘制新进入视口的块。

此来减少内存使用，提高渲染效率，优化重绘制，改善滚动性能。

![分块-Tiling](/images/web_optimize/1/render_tiling.png)

#### 光栅化 - 将图块转为位图

- 光栅化: 合成线程会对**视口附近的图块生成位图**，**以此提高渲染效率**。
- 这一过程需要GPU加速 【例如使用 wil-change、opacity，就会通过 GPU 加速显示】

而渲染进程也维护了一个栅格化的线程池，专门用于将图块转为位图。

#### 画-Draw（合成）- 合成线程计算位置交给GPU呈现

合成线程 **计算每个位图在屏幕上的位置**，交给GPU进行最终呈现。

![画-Draw（合成）](/images/web_optimize/1/render_Draw.png)

而这个整个从绘制-Paint 阶段 生成绘制列表、光栅化、显示的过程，就是我们常说的重绘的过程

完整过程的分工情况如图：

![完整过程的分工](/images/web_optimize/1/render_all.png)

## 相关问题

### 什么是回流/重排（reflow）？

    * reflow 的本质就是重新计算 layout 树。
    * 第一次确定节点的大小和位置，称之为布局（layout）
      * 之后对节点的大小、位置修改重新计算称之为回流
    * "重排/回流"必然导致"重绘"
      * 比如改变一个网页元素的位置，就会同时触发"重排"和"重绘"，因为布局改变了
      * 所以回流是一件很消耗性能的事情
    * 什么属性会导致回流呢？
      * 页面初始渲染
      * 添加/删除可见DOM元素
      * 改变元素位置
      * 改变元素尺寸（宽、高、内外边距、边框等）
      * 改变元素内容（文本或图片等）
      * 改变窗口尺寸
      * 读取元素宽高距离等属性 （并且这些属性 为了获取最新的 尺寸信息，还会**会强制刷新队列，要求样式修改任务立刻执行**）
    * 不同的条件下发生重排的范围及程度会不同
      * 某些情况甚至会重排整个页面，比如滑动滚动条，或者改变窗口尺寸。


#### 浏览器优化

##### 渲染队列机制：
> 渲染队列：当页面中的元素发生改变时，浏览器会把这个操作放入一个队列中，然后根据优先级来依次执行这些操作。

```js
div.style.left = '10px';
div.style.top = '10px';
div.style.width = '10px';
div.style.height = '10px';
```

以上，理论上会发生4次重排 ,但是实际上 因为**渲染队列**机制，**只会发生1次重排**(现代浏览器)

> 浏览器都有 **渲染队列**的机制 ,改变了元素的一个样式会导致浏览器发生**重排或重绘时** ,它会进入一个渲染队列 ,然后浏览器继续往下看，如果下面还有样式修改 ,那么同样入队 ,直到下面没有样式修改 ,浏览器会按**照渲染队列批量执行**来优化重排过程，一并修改样式 ,这样就把本该4次的重排优化为1次



##### 读取元素宽高位置 会导致重排和重绘

```js
div.style.left = '10px';
console.log(div.offsetLeft);

div.style.top = '10px';
console.log(div.offsetTop);

div.style.width = '20px';
console.log(div.offsetWidth);

div.style.height = '20px';
console.log(div.offsetHeight);
```
上面代码会导致**四次重排**和重绘。

> offsetTop、offsetLeft、offsetWidth、offsetHeight
> clientTop、clientLeft、clientWidth、clientHeight
> scrollTop、scrollLeft、scrollWidth、scrollHeight
> getComputedStyle()

这些会**强制刷新队列**,**要求样式修改任务立刻执行**,来获取最新的尺寸数据。

因为浏览器并不确定在下面的代码中是否还有修改同样的样式，为了获取到当前正确的的即时值不得不立刻执行渲染队列触发重排！！！

#### 重绘与重排性能优化

1. 读写分离

```js
div.style.left = '10px';
div.style.top = '10px';
div.style.width = '20px';
div.style.height = '20px';

console.log(div.offsetLeft);
console.log(div.offsetTop);
console.log(div.offsetWidth);
console.log(div.offsetHeight);
// 这样就仅仅发生1次重排了
```

2.样式集中改变

 - 借助 修改 classNaem 的方式 修改样式
 - 借助 cssText 的方式修改样式

```js
// 现代浏览器 因为渲染队列机制 会触发一次重排
div.style.left = '10px';
div.style.top = '10px';
div.style.width = '20px';
div.style.height = '20px';
// 古代浏览器 会触发4次重排
// 需要使用cssText属性合并所有样式改变 减少重排次数 （合并成一次）
div.style.cssText = 'left:10px;top:10px;width:20px;height:20px;';
// 或者 使用 calss 替换 style 修改样式 减少重排次数
div.className = 'new-class';
```

3.缓存布局信息


```js
// 因为读取 元素 尺寸信息 的特性，读写叠加操作造成了 2次重排
div.style.left = div.offsetLeft + 1 + 'px';
div.style.top = div.offsetTop + 1 + 'px';

// 先读取布局信息，然后修改 （ 本质还是读写分离 ）
var curLeft = div.offsetLeft;
var curTop = div.offsetTop;
div.style.left = curLeft + 1 + 'px';
div.style.top = curTop + 1 + 'px';
```

4.元素批量操作

  - 元素脱离文档
  - 改变样式
  - 元素回归文档

```js
// 先隐藏元素，然后修改
var ul = document.getElementById('demo');
ul.style.display = 'none';
for(var i = 0; i < 1e5; i++){
    var li = document.createElement('li');
    var text = document.createTextNode(i);
    li.appendChild(text);
    ul.appendChild(li);
}
ul.style.display = 'block';

// 使用DocumentFragment 文档片段接口（一个没有父对象的最小文档对象）
// 先存储操作后，批量 插入
var ul = document.getElementById('demo');
var frg = document.createDocumentFragment();
for(var i = 0; i < 1e5; i++){
    var li = document.createElement('li');
    var text = document.createTextNode(i);
    li.appendChild(text);
    frg.appendChild(li);
}
ul.appendChild(frg);

// 先克隆在替换
var ul = document.getElementById('demo');
var clone = ul.cloneNode(true);
for(var i = 0; i < 1e5; i++){
    var li = document.createElement('li');
    var text = document.createTextNode(i);
    li.appendChild(text);
    clone.appendChild(li);
}
ul.parentNode.replaceChild(clone,ul);
```

### 什么是重绘 （repaint）

    * repaint 的本质就是**重新根据分层信息计算了绘制指令**。
    * 当第一次渲染内容称之为绘制（paint）
      * 之后重新渲染称之为重绘
    * 重绘不会带来重新布局，所以并不一定伴随重排。
      * 浏览器会根据元素的新属性重新绘制，使元素呈现新的外观。
    * 什么属性会导致重绘呢？
    * `color background box-shadow..` (比如修改背景色、文字颜色、边框颜色、样式等)

### 为什么transform效率高

    因为 transform 既**不会影响布局**也**不会影响绘制指令**，它影响的只是渲染流程的最后一个「draw」阶段
    - 由于 draw 阶段在合成线程中，直接丢给GPU 计算绘制，所以 transform 的变化几乎不会影响渲染主线程。
    - 反之，渲染主线程无论如何忙碌，也不会影响 transform 的变化。

