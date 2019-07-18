# 
<h1 align="center">mcdowell Blog Project</h1>
基于 hexo 的博客项目

## 部署后地址：
github :[https://mcdowell8023.github.io]
coding :[https://mcdowell8023.coding.me]

## 说明：
  - hexo : https://hexo.io/zh-cn/docs
  - 主题 pure: https://github.com/cofess/hexo-theme-pure

> 注： dev 分支是博客项目源码 master 分支 是源码编译后的文件[ 不可合并分支 ]

```bash
  - scaffolds # 模版布局
    - draft.md # 默认模版 draft
    - page.md # 默认模版 page
    - post.md # 默认模版 post
    - tp.md # 自定义模版 tp
  - source # 博客页面文件
    - _data
      - gallery.yml # 描述
      - links.yml # 友情连接 数据
    - _posts # post模版 文章
    - 404
    - about # 关于
    - books # 书籍
    ...
  - themes # 主题
    - landscape # 默认主题
    - pure # 第三方主题 现在使用的
    ... 
    - _config.yml # 主题中的配置文件
  - _config.pure.yml # 针对主题中的配置文件备份
  - _config.yml # 项目主要配置文件
  - .gitignore # 不进行git 管理 文件
  - .gitmodules # 自模块配置【 这里主要用于主题同步 】
  ...
```

## 启动

```bash
  git checkout dev # 切换到dev[dev是博客源码]，master分支用hexo -d [用于部署编译后文件]
  cd themes/pure/
  git submodule init
  git submodule update # 获取我的pure主题的配置

  # rely on

  npm install -g hexo
  npm install hexo-cli -g
  npm install hexo --save
  npm install hexo-server --save
  npm install hexo-deployer-git --save
  npm install

  # start

  hexo s

  # build

  hexo clean # hexo c // 清理
  hexo generate # hexo g
  hexo deploy # hexo d

  hexo d -g  # g为generate 生成，s为本地预览，d为deploy 部署到远程分支
```

## 维护

### 主题文件

```bash
  cd themes/pure/
  git add .
  git commit -m "修改主题文件陪配置"
  git push origin master # 提交到 主题fork后主题的仓库
```

### 提交项目 增加文章后

```bash
  cd ../../ # 切到仓库的根目录
  git add .
  git commit -m "update next settings in blog sources branch"
  git push origin dev # 当前项目在对应dev 分支, master 对应正常访问页面[编译后部署]
```
