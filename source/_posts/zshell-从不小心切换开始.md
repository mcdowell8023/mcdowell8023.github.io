---
title: zshell--从不小心切换开始
comments: true
mathjax: true
categories:
  - 工具
tags:
  - zsh
  - bash
  - adb
date: 2020-04-22 11:48:55
---

每次在 mac 中 打开 iTerm, 提示：

```bash
  The default interactive shell is now zsh.
  To update your account to use zsh, please run `chsh -s /bin/zsh`.
  For more details, please visit https://support.apple.com/kb/HT208050.
```

手贱，执行了`chsh -s /bin/zsh`【其实我都不知道 zeshell 是啥 🤦‍♂️】
然后 `adb connect` 设备的 时候，adb 找不到了！！！🤦‍♂️🤦‍♂️🤦‍♂️
查了一圈，终于有了了解，记录下。

## 关于 shell

Shell 是 Linux/Unix 的一个外壳，你理解成衣服也行。它负责外界与 Linux 内核的交互，接收用户或其他应用程序的命令，然后把这些命令转化成内核能理解的语言，传给内核，内核是真正干活的，干完之后再把结果返回用户或应用程序。

那么关于切换 shell

```bash
  # 查看 电脑几种 shell
  cat /etc/shells

  # 切换 shell
  # 切换成 Zshell
  chsh -s /bin/zsh
  # 切换成 bash
  chsh -s /bin/bash
```

### 回到最初问题

仔细想来，其实就是环境变量问题，上面之所以提示找不到，无非 是 zshell 的环境变量没有配置 adb 需要去配置

> 其实到这里，我仅仅为了使用 adb 切换回去 继续使用 bash 就好，但是： 好奇啊，那么继续

## 关于 Zshell

> Shell 是在程序员与服务器间建立一个桥梁，它对外提供一系列命令，让我们得以控制服务器。常用的 Bash 就是 Shell 的一种，也是 Linux 下默认 Shell 程序。
> Zsh 属于 Shell 中的一种，但比 Bash 好用，而且完全兼容 Bash，拥有及其丰富的插件、强大的自动补全能力、以及自定义功能、代码高亮，可以大大提供我们使用 Linux 的效率。因为 Z 是最后一个字母，因此大家称之为——终极 Shell。

## 安装 Zsh

- mac 跳过， 【系统已经默认安装好了】
- Redhat Linux，执行：`sudo yum install zsh`
- Ubuntu Linux，执行：`sudo apt-get install zsh`
- windows , 洗洗睡吧 👋

### 安装 on-my-zsh

> Zsh 虽然好用，但直接用起来比较麻烦，这就是为啥初期用的人少，后台有个大神，把 Zsh 的配置弄得相当简单 ：(ohmyzsh)[https://github.com/robbyrussell/oh-my-zsh]; 我们就借助 on-my-zsh 进行配置。

#### 自动安装

```bash
  wget https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh -O - | sh
```

#### 手动安装

1. 克隆项目 `git clone git://github.com/robbyrussell/oh-my-zsh.git ~/.oh-my-zsh`
2. 创建.zshrc 配置文件 `cp ~/.oh-my-zsh/templates/zshrc.zsh-template ~/.zshrc`
3. 切换 zsh `chsh -s /bin/zsh` (如果你还没有切换的话)
4. 查看 zsh 版本 `zsh --version`

有人说 还 需要 更新配置 `source ~/.zshrc` ,但是我实际是从新打开 iTerm 就看到 shell 的变化了，代码高亮...

### 配置

#### 主题

`.zshrc` 配置文件 中 默认主题 是 `robbyrussell`

```bash
ZSH_THEME="robbyrussell" # 更换主题名称即可
```

[更多主题](https://github.com/ohmyzsh/ohmyzsh/wiki/themes)

#### 插件

`.zshrc` 配置文件 中 `plugins=(git)` 即时配置插件位置【默认支持 git】

如果 mac 为例 增加 `autojump`

1. 安装 `brew install autojump`
2. 配置 增加

```bash
  plugins=(git autojump)
  [[ -s ~/.autojump/etc/profile.d/autojump.sh ]] && . ~/.autojump/etc/profile.d/autojump.sh
```

> 关于 autojump，他是快捷跳转 你历史目录。比如， 先 `cd /projcet/lanjiang/dist` ,才可以 `j lanjiang`

#### 更多

> 作为一个 前端程序员 ，显然没有运维和服务端人员 shell 玩的溜。能了解的有限可以戳

- [终极 Shell——ZSH](https://zhuanlan.zhihu.com/p/19556676)

## zshell 配置 adb

说会最开始碰到的问题，修正 zshell 环境变量
其实 ，打开 bash 的配置 .bash_profile 对照添加到 .zshrc，然后重启就是了。
我的配置。

```bash
  # 用户配置

  export ANDROID_HOME=$HOME/Library/Android/sdk
  export PATH=$PATH:$ANDROID_HOME/tools
  export PATH=$PATH:$ANDROID_HOME/tools/bin
  export PATH=$PATH:$ANDROID_HOME/platform-tools
  export PATH=$PATH:$ANDROID_HOME/emulator

  export NVM_DIR="/Users/mcdowell/.nvm"
  [ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"  # This loads nvm
```

重启 iTerm `adb version` ,不会在报找不到 adb 了

### 为啥重启，不用 `source .zshrc`

每次 zsh 启动都会读取这个 .zshrc 配置文件，所以我们可以把环境变量的配置信息放到这个配置文件中，这样每次启动 zsh 的时候，就会执行一次环境变量配置信息。

而 bash 修改 配置环境变量后， 必须 倒入环境变量配置文件`source .bash_profile` 才可以生效。

- [zsh 与 bash 设置环境变量的不同点](https://my.oschina.net/liu-xuewei/blog/307647)
