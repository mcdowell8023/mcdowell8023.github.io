---
title: hexo博客-建站
comments: true
categories:
  - 技术帖
tags:
  - hexoBlog
date: 2019-07-18 00:14:13
---

## 安装启动

依赖：Node.js (Should be at least nodejs 6.9) / Git

### 安装 Hexo

```bash
  npm install -g hexo-cli
```

### 建站

```bash
  hexo init <blogName>
  cd <blogName>
  npm install
```

### 目录介绍

```
  .
├── _config.yml # 博客项目配置文件
├── package.json # 依赖包，不多少
├── scaffolds # 文章模版 可以根据现有模版自定义
├── source # 存放用户资源的地方
|   ├── _drafts
|   └── _posts # 文章存放目录
└── themes # 主题
```

> source :
> 资源文件夹是存放用户资源的地方。除 _posts 文件夹之外，开头命名为 _ (下划线)的文件 / 文件夹和隐藏的文件将会被忽略。Markdown 和 HTML 文件会被解析并放到 public 文件夹，而其他文件会被拷贝过去。

### 修改配置

```bash
  vim _config.yml
  # 修改信息
  # Site
  title: # 例如 mcdowell博客
  subtitle: # 例如 博客+笔记
  description: # 例如 javascript/vue/react/node OR 其他 笔记帖以及日常磨叨
  keywords: # 例如 web js vue react nodejs
  author: # 例如 mcdowell
  language: # 例如 zh-CN
  timezone:

```

更多详细配置 见 [hexo](https://hexo.io/zh-cn/docs/configuration)

### 本地启动

```bash
  hexo g # g -> generate 生成静态文件
  hexo s # s -> server 启动项目
```

然后你就见到 你的 项目了

## 更换主题

1. 去 [hexo 官网挑选心仪主题](https://hexo.io/themes/)
2. 找到主题 github 地址

   ```bash
     git clone <github 地址> themes/<主题名称>
     #eg git clone https://github.com/blleng/hexo-theme-lx themes/lx
   ```

3. 修改配置 \_config.yml

   ```bash
    vim _config.yml
    # 修改配置
    theme: <主题名称> # eg: lx
   ```

## 新建文章

### 新建默认模版文章

```bash
  hexo new <title>
```

> 可以通过修改 \_config.yml 中的 default_layout 参数来指定默认布局

### 新建指定模版文章

```bash
  hexo new <模版名称> "文章标题"
  # eg: hexo new photo "My Gallery"
```

## 准备 github

### GitHub 创建个人仓库

1. 准备 github 账号
2. New repository，新建仓库
3. 创建一个和你用户名相同的仓库，后面加.github.io

> 只有这样，将来要部署到 GitHub page 的时候，才会被识别，也就是 xxxx.github.io，其中 xxx 就是你注册 GitHub 的用户名。

### 生成 SSH 添加到 GitHub

#### 本地电脑生成 ssh 密钥

1.  初始化 git 信息

```
  git config --global user.name "yourname"
  git config --global user.email "youremail"
```

2. 生成密钥

```
  ssh-keygen -t rsa -C "youremail"
```

3. 找到.ssh 下的 id_rsa.pub 文件，里面存放的就是密钥信息，可以对应添加到 github

> 生成的 id_rsa 是私钥 生成的 id_rsa.pub 是公钥

#### 将 hexo 部署到 GitHub

修改配置 \_config.yml

```bash
 vim _config.yml
 # 修改配置
 deploy:
 type: git
 repo: https://github.com/YourgithubName/YourgithubName.github.io.git # gitub 地址
 branch: master
```

安装 deploy-git 用于部署

```bash
  npm install hexo-deployer-git --save
```

然后 三部曲

```bash
  hexo clean # 清理
  hexo generate # 生成静态文件
  hexo deploy # 部署推到远端

  # 缩写
  hexo g -d # -> hexo generate --deploy
```

然后浏览器打开 https://YourgithubName.github.io 【YourgithubName 是你的 githu 账户名称】 就可以访问了

### 关于留言功能

- valine :[https://valine.js.org/]
- 为博客添加 Gitalk 评论插件 :[https://www.jianshu.com/p/78c64d07124d]

### 推荐相关连接

[Hexo 博客常用插件及用法](https://blog.cofess.com/2017/08/16/comon-plug-in-and-usage-of-hexo-blog.html#hexo-generator-restful)
[GitHub+Hexo 搭建个人网站详细教程](https://zhuanlan.zhihu.com/p/26625249)
[hexo 史上最全搭建教程](https://blog.csdn.net/sinat_37781304/article/details/82729029)

### 相关文章

- {% post_link hexo博客-常见问题 %}
