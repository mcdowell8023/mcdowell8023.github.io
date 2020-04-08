---
title: 使用scrcpy连接安卓手机进行投屏演示
comments: true
mathjax: true
categories:
  - 工具
tags:
  - 调试
  - 安卓
date: 2020-04-01 15:40:35
---

cd /d d:scrcpy-win64
adb connect 172.16.4.37:5555:11330
scrcpy.exe -s 10.0.1.158:5555 -m 720

第一种 使用 USB 线连接

1. 在手机上启用 USB 调试

2. CMD 窗口输入 adb devices，此时可以看到自己的设备。

PS：无法看到自己设备时，查看手机 USB 调试是否打开；PC 端是否安装手机驱动。

第二种 使用无线 WiFi 连接

首先先了解一点底层的知识：

    Android系统底层运行着一个服务（adbd），用于响应和管理大家在电脑端的adb命令连接，这个服务在启动时会根据手机的配置监听USB连接或网络其中之一。配置的属性为：service.adb.tcp.port，也就是监听的网络端口

以上属性的值 > 0：adbd 将监听网络对应的端口（一般为 5555）
以上属性的值 <=-1：adbd 将监听 USB
接下来是连接步骤：

1. 命令行输入：adb connect 172.16.4.37:5555 (:5555 可省略）

   提示：unable to connect to 172.16.4.37:5555

   解决办法：

   1）手机与 PC 相连，执行以下命令：adb tcpip 5555

   成功提示：restarting in TCP mode port 5555

   如果手机没有和 PC 连接，直接使用以上命令会提示 error:device not found

   然后断开 USB

   2）接着执行 adb connect 172.16.4.37:5555，这时候应该就能连接成功了

   成功提示：connected to 172.16.4.37:5555

2. 输入 adb devices，得到以下提示说明连接成功，可以进行各种调试了

   List of devices attached

   172.16.4.37:5555 devices

3.调试完成之后，输入如下的命令断开连接：

adb disconnect 192.168.1.5:5555 4.最后一步，需要把监听的网络端口重置为-1，监听 USB 端口（好像不做这一步，直接插上 USB 线也能正常使用，而且这一步经常会提示 could not set property，需要 root 权限）

    setprop service.adb.tcp.port -1

    stop adbd

    start adbd
