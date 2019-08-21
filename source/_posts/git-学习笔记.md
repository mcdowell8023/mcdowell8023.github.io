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

```bash
  # 初始化本地仓库 缺省 为当前目录
  $ git init ['your_project'/缺省]
  # git init --bare 建立裸仓库【中心仓库】
```

#### 添加配置

```bash
  # 设置 [本仓库 | 全局 | 系统 ] 的 用户名称
  $ git config [--local | --global | --system] user.name 'Your name'
  # 设置 [本仓库 | 全局 | 系统 ] 的 用户邮箱
  $ git config [--local | --global | --system] user.email 'Your email'
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

### 不进行版本控制

#### 添加不进行版本控制的文件目录

>项目目录下 新建 .gitignore 文件 ，写入不需要进行版本控制的文件名或文件夹

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

#### 提交commit后，想再忽略一些已经提交的文件

  1. 把忽略的文件添加到 .gitignore;
  2. 通过 git rm -- cached < file > 的方式删除掉git仓库里面无需跟踪的文件。





## git 原理
### git 工作解析图

下面这个图展示了工作区、版本库中的暂存区和版本库之间的关系：

![image](https://www.runoob.com/wp-content/uploads/2015/02/1352126739_7909.jpg)

