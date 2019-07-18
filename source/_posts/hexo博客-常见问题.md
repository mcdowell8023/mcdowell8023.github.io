---
title: hexo博客-常见问题
comments: true
categories:
  - 技术帖
tags:
  - hexoBlog
date: 2019-07-18 00:15:05
---

## 博客在 github 访问访问慢？

> 在 coding page 上部署实现国内外分流

1. 申请 coding 账户，新建项目 同 GitHub
2. 添加 ssh key 同 GitHub
3. 修改\_config.yml

```bash
  deploy:
    type: git
    repo:
      github: <github项目地址 url>,master
      coding: <coding项目地址 url>,master
```

4. 部署

```bash
  hexo g
  hexo d
```

5. coding 控制台 开启 pages

> 菜单目录中 -> 代码 pages 服务

## 第三方主题 没有办法上传到自己的项目中？

在使用第三方主题的时候，难免要对主题进行微调。但是调整后的主题文件 是无法上传上去的。这时候，你换台电脑来写博客，就还得再来一遍。【别问我，我是怎么知道的 😂】

其实第三方主题是别人的项目，你修改后自认是无法提交到别人的项目上。

那么针对上面问题，有两个解决办法：

1. 删掉 第三方主题 的.git < 好用但不推荐 >

   > git 不能嵌套上传，最好是显示隐藏文件，检查一下有没有，否则上传的时候会出错，导致你的主题文件无法上传

2. 使用 Git 的 Submodule

- for 主题的仓库到自己的目录下 【喂，for 就不用说了吧】
- 然后先删掉你原来主题文件 【❗️ 备份 ❗ 备份 ️❗️ 备份】

  ```bash
    git rm -r --cached theme/< 主题目录 >
  ```

- 添加 submodlue
  ```bash
    git submodule add < for后的主题仓库地址 > themes/< 主题目录 >
  ```
  > 当前工程根路径下生成一个名为“.gitmodules”的文件
  ```bash
    [submodule "themes/主题"]
    path = themes/主题
    url = https://github.com/wuchong/jacman.git # 主题地址
  ```
- 单独提交修改后的主题【这是你还原主题更改的好时候】

  ```bash

    cd themes/< 主题目录 >
    git add .
    git commit -m "我只是对主题进行了一些调整"
    git push origin master //这是提交到fork后主题的仓库
  ```

- 然后返回博客项目根目录 进行正常提交就好

- 在新电脑上 clone 后 npm install 之前要去下载 主题
  ```bash
    cd themes/< 主题目录 >
    git submodule init
    git submodule update //获取我的NexT主题的配
  ```

## 关于博客版本管理

关于 hexo 博客的机制是这样的：由于 hexo d 上传部署到 github 的 pages 服务其实是 hexo 编译后的文件，是用来生成网页的，不包含源文件。

那么我们写博客的源文件总要放到网上去托管啊。
那么新建仓库，直接上传到 github？
这样算上刚刚 for 的主题，再加上 主页的 pages 服务的主页，一个博客用了三个仓库。貌似有点浪费资源。

那么。。。

> 可以在主页仓库[pages 服务的仓库]的其他分支上传源码

在下面，其实就是 git 的知识了，然而还是贴上吧

```bash
    # 在github仓库上，创建hexo 分支，用于存放源码
    git remote -v #查看远程版本库信息
    git remote add github <url> #添加github远程版本库
    git fetch github hexo #拉取远程版本库
    git merge -h #查看合并帮助信息
    git merge --allow-unrelated-histories github/heox # 对github上的heox分支与本地master分支进行合并（两分支不是父子关系，所以合并需要添加 --allow-unrelated-histories）
    git push github hexo #推送同步到github仓库 的 hexo
```

> 记得 fetch,pull,push 的时候 一定要 带着分支

### 相关连接

[基于 Hexo 的博客同步中的一些问题](https://blog.csdn.net/u010873775/article/details/71303116)

### 相关文章

- {% post_link hexo博客-建站 %}
