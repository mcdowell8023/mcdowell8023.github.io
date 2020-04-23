---
title: web移动端调试笔记
comments: true
mathjax: true
categories:
  - 工具
tags:
  - web移动端
  - 调试
date: 2020-04-22 16:26:08
---

## 常见的几种调试方式

1. Chrome Develop Tool 调试 ：无须额外的成本，方便快捷。但是，真机器和模拟还是不一样的
2. (vConsole 调试)[https://github.com/Tencent/vConsole],使用简单，方便，就是 UI 界面，和 websock 问题，很头疼
3. 苹果调试套装 ：IOS Safari + Mac OS Safari 配合调试 （以下重点说明）
4. 安卓调试套装 ：Android Chrome + 桌面 Chrome 配合调试（以下重点说明）

## IOS Safari + Mac OS Safari 配合调试

### 设置

1. 手机设置: 设置 -> Safari -> 高级 -> Web 检查器

![手机设置](/images/web-dev-tool/ios-setting-1.jpeg)

2. mac 电脑 设置： Safari 启用开发菜单：选择偏好设置 -> 高级 -> 在菜单栏中显示'开发'菜单命令

![mac 电脑 设置](/images/web-dev-tool/ios-setting-2.jpeg)

## 调试

1. 设备与计算机通过数据线连接,手机打开要调试的页面
2. 在 Safari 菜单：开发 -> iphone-> 要调试的页面
   ![mac 电脑 连接 手机](/images/web-dev-tool/ios-dev-1.png)
3. 可以愉快调试了
   ![mac 电脑 开始调试](/images/web-dev-tool/ios-dev-2.png)

## Android Chrome + 桌面 Chrome 配合调试

1. 手机端：开启开发者 -> usb 调试, 用数据线连接至电脑【 你也可以通过 adb 无线调试方式进行连接 】
2. 手机端：安装装谷歌浏览器，并打开
3. 打开控制台，更多 -> `More tools` -> `Remote devices` 【 直接 地址栏 输入 `Chrome://inspect` 】

![mac 电脑 连接 手机](/images/web-dev-tool/Android-dev-1.png)

> 最早在谷歌 75 时候，我是可以直接见到 调试控制 DevTools 页面，更显版本后发现有些不一样。

    谷歌版本>= 81 你可能会碰到，Remote devices 栏中显示 如下：

    ```js
      This panel has been deprecated in favor of the chrome://inspect/#devices interface,which has equivalent functionality
      /*
        直译：
        此面板已被弃用，取而代之的是 chrome://inspect/#devices 接口，它具有同等的功能
      */
      ```
    此时我们可以直接地址栏 输入 `chrome://inspect/#devices` 直接进入 DevTools 页面

3. 找到 RemoteTarget 下 调试设备名称下面，输入 url 地址 open 后（会见到手机打开对应页面），下面会显示对应手机浏览器页签，点击 inspect
   ![mac 电脑 连接 手机](/images/web-dev-tool/Android-dev-2.jpeg)
4. 正常情况下你会见到弹窗 显示手机页面和控制台，就可以进行调试了
   ![mac 电脑 连接 手机](/images/web-dev-tool/Android-dev-3.png)

> 当地 5 步 出现弹窗，你有可能 会碰到 404 或者白页面。别急，打开你的 翻墙，或是 下载离线包具体看这里 [Android 通过 Chrome Inspect 调试 WebView 的 H5 App 出现空白页面的解决方法（不需要 FQ）](https://www.cnblogs.com/slmk/p/7591126.html)

### 关于无线连接方式

> ADB 无线模式调试 具体可以看我的文章 [使用 scrcpy 连接安卓手机进行投屏演示](/2020/04/01/使用scrcpy连接安卓手机进行投屏演示/) 里的无线连接

1. 打开开发者模式，插线，保证同一网络
2. 插上 USB 线，开启 USB 连接，`adb usb`
3. 检查插上的设备，`adb devices`
4. 开启手机服务： `adb tcpip 5555`（PS：5555 是端口号，可以随意地指定）
5. 拔掉数据线，关于手机 找手机 ip 地址 `adb connect 10.0.1.50:5555` [端口可以缺省，需要与 4 一致，默认 5555] # 返回 `connected to 10.0.1.50` 就表示成功
