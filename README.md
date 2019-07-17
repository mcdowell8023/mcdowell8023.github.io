# 
<h1 align="center">mcdowell Blog Project</h1>
基于 hexo 的博客项目

## 说明：
  - hexo : https://hexo.io/zh-cn/docs
  - 主题 pure: https://github.com/cofess/hexo-theme-pure

> 注： dev 分支是博客项目源码 master 分支 是源码编译后的文件[ 不可合并分支 ]

```bash
  - scaffolds
    - 分析页
    - 监控页
    - 工作台
  - 表单页
    - 基础表单页
    - 分步表单页
    - 高级表单页
  - 列表页
    - 查询表格
    - 标准列表
    - 卡片列表
    - 搜索列表（项目/应用/文章）
  - 详情页
    - 基础详情页
    - 高级详情页
  - 用户
    - 用户中心页
    - 用户设置页
  - 结果
    - 成功页
    - 失败页
  - 异常
    - 403 无权限
    - 404 找不到
    - 500 服务器出错
  - 帐户
    - 登录
    - 注册
    - 注册成功
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

### 提交主题文件

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
