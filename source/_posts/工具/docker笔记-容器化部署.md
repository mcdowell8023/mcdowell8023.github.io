---
title: docker笔记-容器化部署
comments: true
mathjax: true
categories:
  - 工具
tags:
  - Docker
  - 笔记
date: 2021-10-28 10:24:19
---
# Docker容器化部署

## 一、什么是Docker

### 1. 容器
- 操作系统层面的虚拟化技术，比虚拟机技术更为轻便、快捷。
- 隔离宿主，隔离其他隔离进程。
  ![](/images/docker/1.png)
### 2. 为啥用
- 更高效地利用系统资源。
- 更快速的启动时间（无需启动操作系统）。
- 一致的运行环境。
- 持续交付和部署（Dockerfile到处运行）。
- 更轻松的迁移。

## 二、Docker容器启动

### 1. 基本概念

#### 1. 仓库
- 存放Docker镜像。

#### 2. 镜像
- 生成Docker容器的依据特殊文件系统。

#### 3. 容器
- 依据镜像生成的运行实体。

[操作教程](https://www.runoob.com/docker/docker-login-command.html)

### 2. 镜像
- login
- pull
- push
- search
- images
- rmi
- tag
- build
- history
- save
- load
- import

### 3. 容器
#### 1. 本地容器操作
- ps
- inspect
- top
- attach
- events
- logs
- wait
- export
- port

#### 2. 容器运行
- run
- start/stop/restart
- kill
- rm
- pause/unpause
- create
- exec

### 4. Dockerfile

  ![](/images/docker/2.png)


## 三、多容器集群管理

### 1. Docker-Compose
- Docker-Compose允许用户通过一个单独的`docker-compose.yml`模板文件（YAML格式）来定义一组相关联的应用容器为一个项目（project），负责实现对Docker容器集群的快速编排。

#### 1. yml文件
- volumes
- healthcheck
    1、volumes
    2、healthcheck
  ![](/images/docker/3.png)



#### 2. 命令
- docker-compose up
- docker-compose build

### 2. Docker Swarm
- Docker容器的集群管理和编排工具。
  ![](/images/docker/4.png)

### 3. Kubernetes
- Docker容器的集群管理和编排工具。Google团队发起的开源项目。

  ![](/images/docker/5.png)

- pod, service, rc
  - pod
    ![](/images/docker/6.png)
    ![](/images/docker/7.png)
  - configmap
  - service
    ![](/images/docker/8.png)
  - 常用命令
    - kubectl get describe create update delete

### 4. 对比
- Docker-Compose
  - 在单服务器或主机上创建和编排多容器。
- Swarm
  - 在多服务器或主机上创建和编排容器集群。
- Kubernetes
  - 在多服务器或主机上创建和编排容器集群。

    ![](/images/docker/9.png)

## 四、项目现状
1. 成本
   - 费用调研
2. Node
   - Node服务
3. Nginx
   - Nginx服务

## 五、最终方案
1. 多容器互联
   - Node容器和Nginx容器互联。

2. 遇到问题
   - CPU，Memory
   - 探针
   - Node优雅退出
   - 首页静态化挂载卷

## 六、去掉Nginx

1. CDN按需回源
   - CDN回源策略
2. 单Node容器