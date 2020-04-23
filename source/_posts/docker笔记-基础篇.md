---
title: docker笔记-基础篇
comments: true
mathjax: true
categories:
  - 工具
tags:
  - Docker
  - 笔记
date: 2019-10-28 10:24:19
---

> 本文仅以前端视角，接触使用 docekr。认识较为肤浅，大神请绕行。

---

<!-- TOC -->

- [关于 Docker](#关于docker)
  - [Docker 是啥](#docker-是啥)
  - [Docker 主要用途](#docker-主要用途)
  - [Docker 中的概念](#docker中的概念)
- [安装](#安装)
  - [慢？！修改镜像源地址](#慢修改镜像源地址)
- [基本操作](#基本操作)
  - [镜像操作](#镜像操作)
    - [检索拉取镜像](#检索拉取镜像)
    - [新增 镜像](#新增-镜像)
    - [删除 镜像](#删除-镜像)
    - [修改 镜像 名称](#修改-镜像-名称)
    - [查看 镜像](#查看-镜像)
    - [导入、导出、上传 镜像](#导入导出上传-镜像)
  - [容器操作](#容器操作)
    - [新增容器](#新增容器)
    - [删除 容器](#删除-容器)
    - [修改 容器 状态](#修改-容器-状态)
    - [进入 容器](#进入-容器)
    - [查看 容器](#查看-容器)
    - [器与主机之间的数据拷贝](#器与主机之间的数据拷贝)
    - [导入、导出 容器](#导入导出-容器)

## <!-- /TOC -->

# 关于 Docker

## Docker 是啥

Docker 属于 Linux 容器的一种封装，提供简单易用的容器使用接口。它是目前最流行的 Linux 容器解决方案。

## Docker 主要用途

- 提供一次性的环境。比如，本地测试他人的软件、持续集成的时候提供单元测试和构建的环境。
- 提供弹性的云服务。因为 Docker 容器可以随开随关，很适合动态扩容和缩容。
- 组建微服务架构。通过多个容器，一台机器可以跑多个服务，因此在本机就可以模拟出微服务架构。

> 以上摘自[阮老师博客](http://www.ruanyifeng.com/blog/2018/02/docker-tutorial.html)

## Docker 中的概念

- 镜像（image）: 镜像中包含有需要运行的文件。镜像用来创建 container，一个镜像可以运行多个 container；镜像可以通过 Dockerfile 创建，也可以从 Docker hub/registry 上下载。
- 容器（container）: 容器是 Docker 的运行组件，启动一个镜像就是一个容器，容器是一个隔离环境，多个容器之间不会相互影响，保证容器中的程序运行在一个相对安全的环境中。
- 仓库（repository）: 共享和管理 Docker 镜像，用户可以上传或者下载上面的镜像，官方地址为 <https://registry.hub.docker.com/>（类似于 github 对源代码的管理），也可以搭建自己私有的 Docker registry。

> 以上摘自[Ant Design Pro 的 Docker 部署方式](https://segmentfault.com/a/1190000019579464)

从我的角度，使用 docker 常常是要解决宿主依赖环境的问题。比如：

- 我是个前端开发从业者，可能有些时候需要在本地 查看 打包编译后的项目真实表现。以前需要本地安装一个 Nginx 配好文件，然后启动。现在，只需要编写好 dockerfile 文件，直接输入命令，就可以在本地查看项目。
- 再者，搭建较为复杂的环境（依赖因为网络等问题过于缓慢），不同开发者需要安装很多东西，不便捷。如，搭建一个 gitbook 文档项目（支持多个文档），从 nodejs ,gitbook, gitbookcli 等比较多的依赖全局安装后，还需要安装 gitbook 的插件（比较慢，而且还没有较快的国内镜像），才可本地启动。毕竟项目本身只是辅助编写文档，因为环境问题给团队带来困扰，就得不偿失了。 如果，将项目的依赖环境做成镜像，放到自己的内部服务器上，每个需要启动项目的人直接使用 docker 镜像，那么基本无痛启动项目。

# 安装

> 安装系统推荐使用 Mac 或者 Linux。
> Windows 不建议使用。《菜鸟教程》的 Docker Windows 安装中说的 Docker toolbox（老黄历） 已经无法使用了。官方推荐 安装 Docker desktop，需要系统是专业版的。（而且折腾一番，及时能用，跑别人写的 Dockerfile 还会碰到问题。过于折腾，本末倒置。）

[Mac 或 Windows 安装软件地址](https://hub.docker.com/?overlay=onboarding)

安装教程：
[Mac 安装](https://docs.docker.com/docker-for-mac/install/)
[ubuntu 安装](https://docs.docker.com/install/linux/docker-ce/ubuntu/)
[Windows 安装](https://docs.docker.com/docker-for-windows/install/)

```bash
  # 查看版本 验证成功
  docker version
  # 把用户加入 Docker 用户组 避免每次命令都输入sudo
  sudo usermod -aG docker $USER

  # 启动一个Nginx 验证效果 正常浏览器输入http://localhost/即可访问
  docker run --detach --publish=80:80 --name=webserver nginx
```

## 慢？！修改镜像源地址

[Docker 修改镜像源地址](https://www.jianshu.com/p/df75f9b5fcf6)

如果是 dockerDesktop 进入设置，找到 Daemon 选项，修改 registry-mirrors 选项为：'<http://hub-mirror.c.163.com'> [这个是网易的加速镜像]，然后 apply && start

> insecure registry 一般 是公司搭建的私服使用

# 基本操作

## 镜像操作

### 检索拉取镜像

```bash
  # 1. 检索镜像 或去 <https://hub.docker.com> 进行搜索
  docker search <Images Name>
  # 2. 从仓库中拉取 镜像 到本地
  docker pull <Images Name:tag-缺省为latest>
```

> 非官方下载 需要加上 仓库地址。
> 如从网易蜂巢的镜像源来下载 ubuntu:18.04 镜像，可以使用如下命令，此时下载的镜像名称为 hub.c.163.com/public/ubuntu:18.04

### 新增 镜像

- 根据现有容器生成镜像

```bash
  # OPTIONS
  # -a: 作者信息；
  # -c: 提交时执行dockerfile指令，包含CMD等
  # -m: 提交消息
  # -p: 提交暂停容器运行
  docker commit [OPTIONS] CONTAINER [镜像名称[:TAG]]
  # 常用方式 填写信息 作者 镜像名称（不包含大写）
  docker commit -m'提交消息' -a '作者' <容器ID> <Images Name:tag>
```

- 本地模版导入

```bash
  # OPTIONS
  # -c: 提交时执行dockerfile指令，包含CMD等
  # -m: 提交消息
  docker import [OPTIONS] file|URL|- <Images Name:tag>
  # eg:
  cat exampleimage.tgz | docker import - exampleimagelocal:new
```

- 基于 dockerfile 创建

```bash
  # 编写好 dockerfile 后 进行build
  # OPTIONS
  # --file , -f Dockerfile的名称（默认为“ PATH / Dockerfile”）
  # --tag , -t 名称以及“ name：tag”格式的标签（可选）
  docker build [OPTIONS] <Dockerfile的 PATH | URL | - >
  # eg:
  # 使用URL github.com/creack/docker-firefox 的 Dockerfile 创建镜像。
  docker build github.com/creack/docker-firefox
  # 使用根目录下的 Dockerfile 创建名称为 lanjiang/gitbook 的镜像
  docker build -t lanjiang/gitbook .
```

### 删除 镜像

- docker rmi 删除

```bash
  # 删除 镜像 (没有使用该镜像的容器 才可删除，不推荐强制删除)
  docker rmi [-f: 强制删除] <Images Name/ids>
  # 筛选包含 none 的镜像删除
  docker rmi $(docker images | grep "none" | awk '{print $3}')
  # 删除全部镜像
  docker rmi $(docker images -a -q)
```

- docker image prune 清除没有被使用的镜像

```bash
  # 清除没有被使用的镜像
  # OPTIONS:
  # -a 删除所有无用镜像（不光临时镜像）；
  # -filter filter:只清理给定过滤的镜像；
  # -f,-force:强制删除
  docker image prune [OPTIONS]
```

### 修改 镜像 名称

```bash
  # 给镜像添加标签
  docekr tag <Images Name/id> <new Images Name>
```

### 查看 镜像

```bash
  # 查看 本地镜像
  docker images / docker image ls

  # 查看 本地全部镜像（包含临时镜像）
  docker images -a
```

### 导入、导出、上传 镜像

```bash
  # 镜像 导出 和 载入
  docker save -o <导出名称> <Images Name:tag>
  # 将镜像 node 生成 /Users/mcdowell/Downloads/ 下的dockderApp.tar
  docker save -o /Users/mcdowell/Downloads/dockderApp.tar node
  # or
  docker save node > /Users/mcdowell/Downloads/dockderApp11.tar
```

```bash
  # 将/Users/mcdowell/Downloads/ 下的tf-keras.tar 镜像 导入镜像
  docker load -i /Users/mcdowell/Downloads/tf-keras.tar
  # or
  docker load < /Users/mcdowell/Downloads/tf-keras.tar
```

```bash
  # 上传镜像
  docker push [OPTIONS] NAME[:TAG]
```

```bash
  # eg:
  # 本地生成镜像
  docker commit c16378f943fe rhel-httpd
  # 给刚生成的镜像打上 源标签
  docker tag rhel-httpd registry-host:5000/myadmin/rhel-httpd
  # 上传镜像
  docker push registry-host:5000/myadmin/rhel-httpd
  # 如果是官网仓库 （官方仓库省略源地址）
  docker push myadmin/rhel-httpd
```

## 容器操作

### 新增容器

- 新建

```bash
  # 新建 容器（默认是停止的）
  docker create <Images Name:version /ID>

  # 用 XX镜像 新建容器 并启动 （ docker create + docker start ）
  # OPTIONS 常用说明
  # -d: 后台运行容器，并返回容器ID；
  # -i: 以交互模式运行容器，通常与 -t 同时使用；
  # -P: 随机端口映射，容器内部端口随机映射到主机的高端口
  # -p: 指定端口映射，格式为：主机(宿主)端口:容器端口
  # -t: 为容器重新分配一个伪输入终端，通常与 -i 同时使用；
  docker run [OPTIONS] <Images Name:version> [COMMAND] [ARG...]


  # 用 node 镜像  以交互模式终端 新建容器 并启动
  docker run -it node bash
  # 用 ubuntu:18.04 镜像  以交互模式终端 新建容器 并启动
  docker run -it ubuntu:18.04 /bin/bash
  # 进入交互模式 使用ctrl+d 或者 exit 退出


  # 用 lanjiang/gitbook镜像 新建容器 并启动 映射到 80 端口
  docker run -p 80:80 lanjiang/gitbook
  # 用 nginx镜像 以守护进行（后台运行）的方式 新建容器 并启动 映射到 80 端口 --默认返回ID号
  docker run -d -p 80:80 nginx

  # 使用docker镜像nginx:latest 以后 随机端口 台模式 启动一个容器,并将容器命名为mynginx【需要使用docker port 才可访问】
  docker run -P -d --name mynginx  nginx:latest
```

### 删除 容器

- docker rm 删除

```bash
  # 删除容器 id可以是多个 （容器必须终止才能删除）
  docker rm <IDs>

  # 筛选包含 gitbook 的容器删除
  docker rm $(docker ps -a|grep gitbook|awk '{print $1}')
  # 删除 全部容器
  docker rm $(docker ps -a -q)

```

- docker container prune 删除

```bash
  # 删除暂停的容器
  # OPTIONS
  # --filter 提供过滤器值（例如'until ='）
  # --force , -f 强制删除，不提示
  docker container prune [OPTIONS]
```

### 修改 容器 状态

- 启动、重启

```bash
  # 启动 容器
  docker start <Images Name:version /ID>

  # 重启 容器
  docker restart <ID>
```

- 暂停\恢复 容器 (保存容器状态）

```bash
  # 暂停 容器
  docker pause <ID>
  # 恢复 容器
  docker unpause <ID>
```

- 终止容器

```bash
  # 终止容器 id可以是多个 kill 也可以
  docker stop <IDs>
  # 筛选包含 gitbook 的容器终止
  docker stop $(docker ps -a|grep gitbook|awk '{print $1}')

```

### 进入 容器

- docker attach 进入 容器

```bash
  # 默认 CTRL-c不退出当前终端而是 给容器内 程序执行
  docker attach [OPTIONS] <container Name/ID>
  # 使用 --sig-proxy=false 避免上述问题
  docker attach --sig-proxy=false mynginx
  # 缺点：多个窗口执行时，一个窗口阻塞，其他窗口无法执行
```

- docker exec 进入 容器

```bash
  # 使用 exec 进入容器
  #OPTIONS说明：
  # -d :分离模式: 在后台运行
  # -i :即使没有附加也保持STDIN 打开
  # -t :分配一个伪终端
  docker exec [OPTIONS] <container Name/ID> [命令] [ARG...]
  # 在容器 mynginx 中以交互模式执行容器内 /root/blue.sh 脚本:
  docker exec -it mynginx /bin/sh /root/blue.sh

  # 在运行的容器中 以 以交互模式终端 执行 bash 命令
  docker exec -it <container Name/ID> /bin/bash
```

### 查看 容器

- 查看本地容器

```bash
  # 输出 ID容器的 日志
  docker logs <ID>

  # 查看 运行的 容器
  docker ps
  # 查看全部容器（包含运行和暂停的）
  docker ps -a # -q:只显示ID

```

- 查看容器内部信息

```bash
  # 查看容器详情
  docker inspect <container Name/ID>
  # 查看容器内 进程（容器需要启动）
  docker top <container Name/ID>
  # 查看统计信息 （cpu 内存 存储 网络使用情况）
  docker stats <container Name/ID>

  # 查看系统变更
  docker diff <container Name/ID>

  # 查看 容器 端口映射
  docker port <container Name/ID>
```

### 器与主机之间的数据拷贝

```bash
  # 容器与主机之间的数据拷贝
  docker cp <path> <path>
  # 将主机/www/blue目录拷贝到容器63d47d7c0d4c的/www目录下。
  docker cp /www/blue 63d47d7c0d4c:/www/
  # 将主机/www/blue目录拷贝到容器 63d47d7c0d4c中，目录重命名为www(此处无斜杠))
  docker cp /www/blue 63d47d7c0d4c:/www
  # 将容器 63d47d7c0d4c 的 /www 目录拷贝到主机的 /tmp 目录中。
  docker cp  63d47d7c0d4c:/www /tmp/
```

### 导入、导出 容器

```bash
  # 容器导出
  docker export -o tf-keras-33f6c8359187.tar <container Name/ID>
  # or
  docker export 33f6c8359187 > tf-keras-33f6c8359187.tar
  # 导入
  docker import tf-keras-33f6c8359187.tar
```

> 与镜像导出（docker save）比较：
> 容器快照文件 将丢弃所有的历史记录和元数据信息（即仅保存容器当时的快照状态），而镜像存储文件将保存完整记录，体积更大
