---
title: Docker笔记(一)
comments: true
mathjax: true
categories:
  - 技术帖
tags:
  - Docker
  - 笔记
date: 2019-10-25 10:24:19
---

# Docker笔记 (一)

## 介绍

> 本文仅以前端视角，接触使用docekr。认识较为肤浅，大神请绕行。

Docker总听人说起，从来没接触。在公司后台改在部署方式时，半推半就的开始接触了Docker。紧紧在此做些记录。

### Docker 是啥

Docker 属于 Linux 容器的一种封装，提供简单易用的容器使用接口。它是目前最流行的 Linux 容器解决方案。

### Docker 主要用途

- 提供一次性的环境。比如，本地测试他人的软件、持续集成的时候提供单元测试和构建的环境。
- 提供弹性的云服务。因为 Docker 容器可以随开随关，很适合动态扩容和缩容。
- 组建微服务架构。通过多个容器，一台机器可以跑多个服务，因此在本机就可以模拟出微服务架构。

> 以上摘自[阮老师博客](http://www.ruanyifeng.com/blog/2018/02/docker-tutorial.html)

### Docker中的概念

- 镜像（image）: 镜像中包含有需要运行的文件。镜像用来创建container，一个镜像可以运行多个container；镜像可以通过Dockerfile创建，也可以从Docker hub/registry上下载。
- 容器（container）: 容器是Docker的运行组件，启动一个镜像就是一个容器，容器是一个隔离环境，多个容器之间不会相互影响，保证容器中的程序运行在一个相对安全的环境中。
- 仓库（repository）: 共享和管理Docker镜像，用户可以上传或者下载上面的镜像，官方地址为 https://registry.hub.docker.com/ （类似于github对源代码的管理），也可以搭建自己私有的Docker registry。

> 以上摘自[Ant Design Pro 的 Docker 部署方式](https://segmentfault.com/a/1190000019579464)

### 关于Docker

从我的角度，使用 docker 常常是要解决宿主依赖环境的问题。比如：

- 我是个前端开发从业者，可能有些时候需要在本地 查看 打包编译后的项目真实表现。以前需要本地安装一个Nginx 配好文件，然后启动。现在，只需要编写好dockerfile文件，直接输入命令，就可以在本地查看项目。
- 再者，搭建较为复杂的环境（依赖因为网络等问题过于缓慢），不同开发者需要安装很多东西，不便捷。如，搭建一个gitbook文档项目（支持多个文档），从nodejs ,gitbook, gitbookcli等比较多的依赖全局安装后，还需要安装 gitbook的插件（比较慢，而且还没有较快的国内镜像），才可本地启动。毕竟项目本身只是辅助编写文档，因为环境问题给团队带来困扰，就得不偿失了。 如果，将项目的依赖环境做成镜像，放到自己的内部服务器上，每个需要启动项目的人直接使用docker镜像，那么基本无痛启动项目。

## 安装

> 安装系统推荐使用 Mac 或者 Linux。
> Windows不建议使用。《菜鸟教程》的Docker Windows 安装中说的 Docker toolbox（老黄历） 已经无法使用了。官方推荐 安装Docker desktop，需要系统是专业版的。（而且折腾一番，及时能用，跑别人写的Dockerfile 还会碰到问题。过于折腾，本末倒置。）

[Mac或Windows安装软件地址](https://hub.docker.com/?overlay=onboarding)

安装教程：
[Mac安装](https://docs.docker.com/docker-for-mac/install/)
[ubuntu安装](https://docs.docker.com/install/linux/docker-ce/ubuntu/)
[Windows安装](https://docs.docker.com/docker-for-windows/install/)

我在本地主要是使用 Mac 编写dockerfile, 使用docker的。

```bash
  # 查看版本 验证成功
  docker version
  # 把用户加入 Docker 用户组 避免每次命令都输入sudo
  sudo usermod -aG docker $USER

  # 启动一个Nginx 验证效果 正常浏览器输入http://localhost/即可访问
  docker run --detach --publish=80:80 --name=webserver nginx
```

## 基本操作

### 镜像操作

```bash
  # 检索镜像 或去 https://hub.docker.com 进行搜索
  docker search <Images Name>
  # 从仓库中拉取 镜像 到本地
  docker pull <Images Name:tag-缺省为latest>
  # 非官方下载 需要加上 仓库地址。
  # 如从网易蜂巢的镜像源来下载ubuntu:18.04镜像，可以使用如下命令，此时下载的镜像名称为hub.c.163.com/public/ubuntu:18.04


  # 查看全部镜像
  docker images -a
  # 筛选包含 none 的镜像删除
  docker rmi $(docker images | grep "none" | awk '{print $3}')
  # 删除全部镜像
  docker rmi $(docker images -a -q)
```

### 容器操作

#### 常用命令

```bash
  # 新建 容器（默认是停止的）
  docker create <Images Name:version /ID>
  # 启动 容器
  docker start <Images Name:version /ID>
  # 在运行的容器中 以 以交互模式终端 执行 bash 命令
  docker exec -it <container Name/ID> /bin/bash

  # 用 XX镜像 新建容器 并启动 （ docker create + docker start ）
  docker run <Images Name:version>


  # 用 node 镜像  以交互模式终端 新建容器 并启动
  docker run -it node bash
  # 用 ubuntu:18.04 镜像  以交互模式终端 新建容器 并启动
  docker run -it ubuntu:18.04 /bin/bash
  # 进入交互模式 使用ctrl+d 或者 exit 退出


  # 用 lanjiang/gitbook镜像 新建容器 并启动 映射到 80 端口
  docker run -p 80:80 lanjiang/gitbook
  # 用 nginx镜像 以守护进行（后台运行）的方式 新建容器 并启动 映射到 80 端口 --默认返回ID号
  docker run -d -p 80:80 nginx
  # 输出 ID容器的 日志
  docker logs <ID>

 

  # 查看 运行的 容器
  docker ps
  # 查看全部容器（包含运行和暂停的）
  docker ps -a # -q:只显示ID

  # 停止容器 id可以是多个 kill 也可以
  docker stop <IDs>  
  # 筛选包含 gitbook 的容器停止
  docker stop $(docker ps -a|grep gitbook|awk '{print $1}')

  # 删除容器 id可以是多个 （容器必须停止才能删除）
  docker stop <IDs>

  # 筛选包含 gitbook 的容器删除
  docker rm $(docker ps -a|grep gitbook|awk '{print $1}')
  # 删除 全部容器
  docker rm $(docker ps -a -q)
```

```bash
 # 根据 dockfile 打包镜像 名称为
  docker build -t lanjiang/gitbook .
```


