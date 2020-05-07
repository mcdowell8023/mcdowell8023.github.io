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

## 需要演示？手机投屏到电脑上？

> 快快使用 scrcpy 远程显示/控制 Android 手机

[scrcpy-github](https://github.com/Genymobile/scrcpy)

## 安装

> 要求 Android 5.0 以上，电脑安装 adb，手机启用 adb 调试，开发者模式

### windows

[scrcpy-win64-v1.12.1.zip](https://github-production-release-asset-2e65be.s3.amazonaws.com/111583593/0a858b80-1b71-11ea-9a93-d063f7e969c9?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAIWNJYAX4CSVEH53A%2F20200421%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20200421T114947Z&X-Amz-Expires=300&X-Amz-Signature=4d120c68c74fb5e4647f67986c6a000d4ffc4cd917a5d56ce3db7f655876c1fd&X-Amz-SignedHeaders=host&actor_id=19951427&repo_id=111583593&response-content-disposition=attachment%3B%20filename%3Dscrcpy-win64-v1.12.1.zip&response-content-type=application%2Foctet-stream)

### macOS

```bash
  brew install scrcpy
  brew cask install android-platform-tools # 如果 有了adb 不需要安装
```

## 使用

[github scrcpy](https://github.com/Genymobile/scrcpy)

### mac 启动

```bash
  # 启动
  scrcpy

  # 帮助
  scrcpy --help
```

### windows 启动

```bash
# 先进到对应目录
cd /d d:scrcpy-win64
adb connect 172.16.4.37:5555:11330
scrcpy.exe -s 10.0.1.158:5555 -m 720
```

### 使用 USB 线连接

1. 在手机上启用 USB 调试
2. `adb devices` 此时可以看到自己的设备。
3. `scrcpy` 即可看到 手机屏幕投射到 电脑
4. control + c 退出

> 无法看到自己设备时，查看手机 USB 调试是否打开；PC 端是否安装手机驱动。

#### windos

需要先进入

### 使用无线 WiFi 连接

#### ADB 无线模式调试

1. 打开开发者模式，插线，保证同一网络
2. 插上 USB 线，开启 USB 连接，`adb usb`
3. 检查插上的设备，`adb devices`
4. 开启手机服务： `adb tcpip 5555`（PS：5555 是端口号，可以随意地指定）
5. 拔掉数据线，关于手机 找手机 ip 地址
6. `adb connect 10.0.1.50:5555` [端口可以缺省，需要与 4 一致，默认 5555] # 返回 connected to 10.0.1.50 就表示成功

7. `scrcpy -s 10.0.1.50:5555 -m 720` # 启动 10.0.1.50:5555 的设备 以 720 分辨率 显示【分辨率越大，越卡】

> 据说，你的手机设备每次重启后，你可能需要重复 1-6 【 🥱 好在我的手机一般不重启 】

## 安卓手机连接命令

```shell
  # 进入对象ip 手机 shell 命令
  adb -s 10.0.1.50 shell
  # 进入 adb shell 命令
  # 查看手机日志
  logcat ｜ grep 'TAG'
  # 过滤 TAG 日志

  # 安装apk 需要 打开 允许usb 安装
  adb install -r <apk路径>

```
