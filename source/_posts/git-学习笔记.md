---
title: git 学习笔记
comments: true
mathjax: true
categories:
  - 技术帖
tags:
  - git
  - 笔记
date: 2019-08-21 16:37:24
---

## 安装 git

> 已有？请跳过

- [git 文档地址](https://git-scm.com/book/zh/v2)
- [git 下载地址](https://git-scm.com/downloads)
- 查看 git 版本

```bash
  $ git --version
```

## 初始化

### 基本配置

#### 配置帐号信息

```bash
  # 设置 [本仓库 | 全局 | 系统 ] 的 用户名称
  $ git config [--local | --global | --system] user.name 'Your name'
  # 设置 [本仓库 | 全局 | 系统 ] 的 用户邮箱
  $ git config [--local | --global | --system] user.email 'Your email'
```

> 当然你也可以通过修改 .git 文件目录下面的 config 文件进行修改

#### 配置别名

```bash
  $ git config --global alias.st status #git st
  $ git config --global alias.co checkout #git co
  $ git config --global alias.br branch #git br
  $ git config --global alias.ci commit #git ci
```

#### 查看配置

```bash
  # 查看配置
  git config --list [--local | --global | --system]
  # 根据git哈希值 查看内容 / 查看类型
  git cat-flie [-p / -t]

```

#### 参数说明

- local：区域为本仓库
- global: 当前用户的所有仓库
- system: 本系统的所有用户

> 缺省等同于 local
> 优先级：local > global > system

### 生成 shh 密钥

> 如果你想通过 https 的方式 每次提交输入密码，当然可以跳过此节

```bash
  # 生成秘钥（根据电脑主机）
  $ ssh-keygen -t rsa

  # 根据邮箱生成 密钥
  $ ssh-keygen -t rsa -C "youremail"
```

> 找到.ssh 下的 id_rsa.pub 文件，里面存放的就是密钥信息，可以对应添加到 远端 github 或者 gitlab 中

### 获取 Git 仓库

#### 在现有目录中初始化仓库

```bash
  # 初始化本地仓库 缺省 为当前目录
  $ git init ['your_project'/缺省]
  # git init --bare 建立裸仓库【中心仓库】
  # 获取状态
  $ git status
  # .或*代表全部添加
  $ git add [file1] [file2] ...
  # 提交
  $ git commit -m "提交message"
  # 添加远程源
  $ git remote add origin [远程地址]
  # push 同时 设置默认跟踪分支
  $ git push -u origin master
```

#### 克隆现有的仓库

```bash
  # 克隆远程仓库到本地  fileName 的文件夹内 【缺省-默认远端名称】
  $ git clone [url] <fileName/缺省>
```

### 不进行版本控制

#### 添加不进行版本控制的文件目录

> 项目目录下 新建 .gitignore 文件 ，写入不需要进行版本控制的文件名或文件夹

```bash
// 文件示例
  node_modules # 对 node_modules文件夹及其文件 及 node_modules文件 不进行版本控制

  doc   # 对 [doc文件夹及其子文件 和 名称为doc的文件] 不进行版本控制
  *doc # 不允许 任何 包含doc字符的 文件夹及其子文件 和 文件（包括.扩展名）不进行版本控制

  doc/   # 对 [doc文件夹及其下文件] 不进行版本控制
  *doc/  # 对 [任何 包含doc字符的文件夹及其下文件] 不进行版本控制

  *.md // 对 .md结尾的文件 不进行版本控制

  /* 复合示例 */
  doc
  !doc/* # git管doc文件夹，不管doc文件

```

#### 提交 commit 后，想再忽略一些已经提交的文件

1. 把忽略的文件添加到 .gitignore;
2. 通过 git rm -- cached < file > 的方式删除掉 git 仓库里面无需跟踪的文件。

## 实操

### 更新

#### git pull

> git pull 相当于从远程获取最新版本并 merge 到本地

```bash
  # 按照git branch 设置的默认跟踪的服务器和分支来拉取
  $ git pull
  # 实例：拉取远程服务器origin的master分支
  $ git pull origin master
```

git pull 详解

```bash
  $ git pull [<options/缺省>] [<远端仓库名称> [<分支名称>…​]]
  # options : –allow-unrelated-histories 允许无关的历史，这个选项，更多是在更改远程仓库的时候用到
  # options : –ff     开启fast-forward
  # options : –no-ff  强行关闭fast-forward方式
  # options : –ff-only
```

#### git fetch

> git fetch 相当于是从远程获取最新到本地，不会自动 merge
> 在实际使用中，git fetch 更安全一些

使用 git fetch 进行更新

```bash
  # 将远程仓库的master分支下载到本地当前branch中
  $ git fetch orgin master

  # 比较本地的master分支和origin/master分支的差别
　$ git log -p master  ..origin/master

　# 进行合并
  $ git merge origin/master
```

OR

```bash
  # 从远程仓库master分支获取最新，在本地建立tmp分支
  $ git fetch origin master:tmp
  #將當前分支和tmp進行對比
  $ git diff tmp
  #合并tmp分支到当前分支
  $ git merge tmp
```

> git pull = git fetch + git merge

### 提交

#### 普通新建提交

```bash
  # 查看提交状态【是否有未提交】（不必的）
  # $ git status # 没有未提交的显示 nothing to commit

  # 添加新文件
  $ git add ['文件名' | '.'代表全部]

  # 提交填写日志
  $ git commit -m'这里填写提交日志'

  # 查看日志(不必的)
  # $ git log

  # 更新 先更新避免发生冲突
  $ git pull
  # 推送到 orgin 远端服务器
  $ git push

```

#### git add 详解

> 将修改添加到暂存区

```bash
  # 将所有修改添加到暂存区
  $ git add .
  # Ant风格添加修改
  $ git add *

  # 将文件的修改、文件的删除，添加到暂存区。
  $ git add -u
  # 将文件的修改，文件的删除，文件的新建，添加到暂存区
  $ git add -A

  # 将以Controller结尾的文件的所有修改添加到暂存区
  $ git add *Controller
  # 将所有以Hello开头的文件的修改添加到暂存区 例如:HelloWorld.txt,Hello.java,HelloGit.txt ...
  $ git add Hello*
  # 将以Hello开头后面只有一位的文件的修改提交到暂存区 例如:Hello1.txt,HelloA.java 如果是HelloGit.txt或者Hello.java是不会被添加的
  $ git add Hello?

```

#### git push

> 将修改添加到暂存区

##### 语法

```bash
  # 命令用于将本地分支的更新，推送到远程主机。与git pull命令相似。
  $ git push <远程主机名/缺省> <本地分支名/缺省>:<远程分支名/缺省>
```

##### 示例

###### 完整示例

```bash
  # 将本地的master分支推送到origin主机的master分支。如果master不存在，则会被新建。
  $ git push origin master

```

###### 本地分支名 缺省

表示**删除指定的远程分支**，因为这等同于推送一个空的本地分支到远程分支。

```bash
  # 表示删除origin主机的master分支
  $ git push origin :master
  # 等同于
  $ git push origin --delete master
```

###### 本地分支、远程分支都 缺省

表示推送**特定主机的对应分支**简写方式：如果当前分支与远程分支之间存在追踪关系，则本地分支和远程分支都可以省略。

```bash
  # 将当前分支推送到origin主机的对应分支
  $ git push origin
```

###### 远程主机名、本地分支、远程分支都 缺省

表示推送**origin 主机的对应分支**简写方式：如果当前分支只有一个追踪分支，那么主机名都可以省略。

```bash
  $ git push
```

当前分支与多个主机存在追踪关系，则可以使用-u 选项指定一个默认主机，这样后面就可以不加任何参数使用 git push

```bash
  # 将本地的master分支推送到origin主机，同时指定origin为默认主机，后面就可以不加任何参数使用git push了
  $ git push -u origin master
```

### 常见操作

#### 查看日志

#### 回退版本

## git 原理

### git 工作解析图

下面这个图展示了工作区、版本库中的暂存区和版本库之间的关系：

![image](https://www.runoob.com/wp-content/uploads/2015/02/1352126739_7909.jpg)

### fast-forward [快进] 与 no-fast-forward

> 如果是正常 push 到仓库，正确的完成 commit 更新，那么这次更新就是一个 fast-forward 更新,而如果不理会错误警告用本地更新强制覆盖仓库，就是一次 no-fast-forward 更新，很明显，no-fast-forward 更新会导致记录丢失

具体阅读以下文章，感受下：

- [ git：到底什么是 fast-forwards ? ](https://www.jianshu.com/p/86c44bef4ea1)
- [图解 4 种 git 合并分支方法 - 颜海镜](https://yanhaijing.com/git/2017/07/14/four-method-for-git-merge/)

### git 文件目录

- COMMIT_EDITMSG
- config 当前 git 的配置文件
- description （仓库的描述信息文件）
- HEAD （指向当前所在的分支），例如当前在 develop 分支，实际指向地址是 refs/heads/develop
- hooks [文件夹]
- index
- info [文件夹]
- logs [文件夹]
- objects [文件夹] （存放所有的 git 对象，对象哈希值前 2 位作为文件夹名称，后 38 位作为对象文件名, 可通过 git cat-file -p 命令，拼接文件夹名称+文件名查看）
- ORIG_HEAD
- refs [文件夹]
  • heads （存放当前项目的所有分支）
  • tags (存放的当前项目的所有标签，又叫做里程碑)

#### commit 与 tree 和 blob 的关系

[![关系图谱](https://s2.ax1x.com/2019/04/23/EAtpLR.th.png)](https://imgchr.com/i/EAtpLR)

```bash
  commit 对应一个tree

  tree 包含文件[blob]，如果下级还是文件夹，则又是一个tree

  blob 是tree下面具体文件 【与文件名无关具体到文件内容】
```
