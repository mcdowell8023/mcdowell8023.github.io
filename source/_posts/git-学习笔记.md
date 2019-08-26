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

## 目录

[说明文字](#test)

## 安装 git

> 已有？请跳过

- [git 文档地址](https://git-scm.com/book/zh/v2)
- [git 下载地址](https://git-scm.com/downloads)
- 查看 git 版本

```bash
  git --version
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
  # 配置别名
  $ git config --global alias.st status # git st
  $ git config --global alias.co checkout # git co
  $ git config --global alias.br branch # git br
  $ git config --global alias.ci commit # git ci

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

- 检查是否已经存在公私钥

```bash
  # 查看.ssh目录下是否有密钥 如列表中包含 id_rsa和id_rsa.pub 说明电脑中已经存在公私钥
  ls -al ~/.ssh

  # cat ~/.ssh/id_rsa.pub
```

- 生成密钥

```bash
  # 生成秘钥（根据电脑主机）
  $ ssh-keygen -t rsa

  # 根据邮箱生成 密钥
  $ ssh-keygen -t rsa -C "youremail"
  # 执行后 ：建议不要输入，一路回车，
```

> 生成的 id_rsa 是私钥 生成的 id_rsa.pub 是公钥

- 添加 密钥

在对应服务器增加密钥。【github -> setting -> SSH and GPG keys】

> 如果公司是自己搭建的 git 服务（未使用 gitlab），则交给管理员添加。如果使用 gitlab 在设置增加密钥

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

## 常见操作

### 查看信息

#### 查看帮助 [ git help ]

```bash
    # 所有可用的命令都将打印在标准输出上
    $ git help [--all/-a]

    # 在标准输出中也会列出有用的Git指南
    $ git help [--guide/-g]

    # 显示 git 手册页
    $ git help git
```

#### 查看状态 [ git status ]

> git status 命令用于显示工作目录和暂存区的状态。git status 不显示已经 commit 到项目历史中去的信息。看项目历史的信息要使用 git log

常用于 git commit 之前, 这样能防止你不小心提交了您不想提交的东西。

```bash
  # 显示工作目录和暂存区的状态
  $ git status

  # 只列出所有已经被git管理的且被修改但没提交的文件
  $ git status -uno
  # 紧凑的格式输出
  $ git status -s # or $ git status --short
```

#### 查看变化 [ git diff ]

> 对比 修改之后还没有暂存起来的内容变化

```bash
  # 工作树中的更改尚未分段进行下一次提交
  $ git diff
  # 比较当前文件和暂存区文件差异 git diff
  $ git diff <file>

  $ git diff <id1><id1><id2> # 比较两次提交之间的差异

  $ git diff <branch1> <branch2> # 在两个分支之间比较
  $ git diff --staged # 比较暂存区和版本库差异

  $ git diff --cached # 比较暂存区和版本库差异

  $ git diff --stat # 仅仅比较统计信息

  $ git diff HEAD # 显示工作版本(Working tree)和HEAD的差别

  $ git diff test # 查看当前目录和另外一个分支(test)的差别
```

#### 查看日志 [ git log ]

```bash
  # 查看所有分支的历史 git log 等同于它
  $ git log --all
  # 查看图形化的 log 地址
  $ git log --all --graph
  # 查看单行的简洁历史。
  $ git log --oneline
  # 查看最近的四条简洁历史。
  $ git log --oneline -n4
  #查看所有分支最近 4 条单行的图形化历史。
  $ git log --oneline --all -n4 --graph
  # 跳转到git log 的帮助文档网页
  $ git help --web log

  $ git log -1 // 最近一次提交信息

```

#### 查看某个文件的版本历史 [ git show ]

```bash
    // 先查看文件提交历史
    git log --pretty=oneline [文件名或文件路径] // 例如src/AfterView/common/commonStream.vue

    git show [版本号] // 显示具体的某次的改动的修改
```

#### 图形化查看提交内容 [ gitk ]

```bash
    # 在当前目录下输入，弹出图形化界面
    $ gitk

    $ gitk --all
```

> 定制化图形界面 ： view --> new view [勾选 all refs] # 显示全部分支

### 更新

- 使用 git pull 进行更新

```bash
  # 按照git branch 设置的默认跟踪的服务器和分支来拉取
  $ git pull
  # 实例：拉取远程服务器origin的master分支
  $ git pull origin master
```

- 使用 git fetch 进行更新

```bash
  # 将远程仓库的master分支下载到本地当前branch中
  $ git fetch orgin master

  # 比较本地的master分支和origin/master分支的差别
　$ git log -p master  ..origin/master

　# 进行合并
  $ git merge origin/master
```

### 文件重命名

- 先删除文件再添加文件

```bash
  # 重命名3步骤    eg: 重命名 readme 为 readme.md
  $ mv [文件名1] [文件名2]  # 重命名文件名1为文件名2
  $ git add ['文件名'] # 添加新文件 eg: git add readme.md
  $ git rm ['文件名'] # 删除旧文件 eg: git rm readme

  # 就可以正常commit

```

- 直接使用 git 命令

```bash
  $ git mv ['旧文件名'] ['新文件名'] # git重命名（相当于上面三个步骤）

  # 就可以正常commit
```

```bash
  # 回滚到对版本号的应提交状态【缺省回滚到最近的一次提交】
  $ git reset --hard [提交版本号|缺省]

  # 记录所有HEAD的历史，也就是说当你做reset，checkout等操作的时候，这些操作会被记录在reflog中
  $ git reflog
```

### 文件删除

- 先删除文件再添加文件

```bash
  $ rm <file> # 删除文件
  $ git add ['文件名'] # 添加新文件 eg: git add readme.md

  # 就可以正常commit

```

- 直接使用 git 命令

```bash
  # 删除工作区，暂存区 的文件
  $ git rm <file>
```

> 如果删除出错 借助 git reset --hard 进行撤销

### 提交

```bash
  # 添加新文件
  $ git add ['文件名' | '.'代表全部]

  # 查看提交状态【是否有未提交】（不必的） but 能防止你不小心提交了您不想提交的东西
  # $ git status # 没有未提交的显示 nothing to commit

  # 提交填写日志
  $ git commit -m'这里填写提交日志'

  # 如果发现点问题，那么继续进行了修改
  # 继续执行 git add . 重新提交，会覆盖上次提交，只以当前为准
  # $ git commit --amend

  # 查看日志(不必的)
  # $ git log

  # 更新 先更新避免发生冲突
  $ git pull
  # 推送到 orgin 远端服务器
  $ git push

```

### 撤销&回滚

#### 撤销

##### 修改文件 尚未提交

- 未执行 git add 操作

```bash
  # 从暂存区（index） 恢复 文件
  $ git checkout <fileName>
  $ git checkout .
```

- 文件执行了 git add 操作，恢复 文件（index 内回滚）

```bash
  # 取消暂存
  $ git reset HEAD fileName
  # 撤销修改
  $ git checkout < fileName >
```

- 同时对多个文件执行了 git add 操作，本次只想提交其中一部分文件

```bash
  $ git add *
  $ git status
  # 取消暂存
  $ git reset HEAD <filename>
```

##### 修改文件 已经提交（git commit）到 本地仓库

- 修改 git commit 不再产生新的 Commit[ 只能修改最近一次 ]

```bash
  # 修改最后一次提交
  $ git add sample.txt
  # 代替（或这说修改）上一次提交，不只是修改message。
  $ git commit --amend -m"说明"
```

- 多次 git commit 撤销到其中某次 Commit

```bash
  # 修改最后一次提交
  $ git reset --[soft/hard/mixed] [commit|HEAD]

  # 重置暂存区的指定文件，与上一次commit保持一致，但工作区不变
  $ git reset [file]

  # 重置暂存区与工作区，与上一次commit保持一致
  $ git reset --hard

  # 重置当前分支的指针为指定commit，同时重置暂存区，但工作区不变
  $ git reset [commit]

  # 重置当前分支的HEAD为指定commit，同时重置暂存区和工作区，与指定commit一致
  $ git reset --hard [commit]
```

#### 回滚

> 已进行 git push，即已推送到“远程仓库”中。我们将已被提交到“远程仓库”的代码还原操作叫做“回滚”！
> 注意：对远程仓库做回滚操作是有风险的，需提前做好备份和通知其他团队成员！

##### 还原 远端服务器 提交的代码

```bash
  # 根据 tag 还原工作区代码
  $ git checkout <tag>

  # 回滚到指定commitID
  $ git checkout <commitID> <filename>
```

##### 删除最后一次远程提交

- 使用 revert

```
  $ git revert HEAD
  $ git push origin master
```

- 使用 reset

```
  $ git reset --hard HEAD^
  $ git push origin master -f
```

二者区别：

> revert 是放弃指定提交的修改，但是会生成一次新的提交，需要填写提交注释，以前的历史记录都在；
> reset 是指将 HEAD 指针指到指定提交，历史记录中不会出现放弃的提交记录。

##### 回滚某次提交

```bash
  # 找到要回滚的 commitID
  $ git log
  # 根据 commitID 进行回滚
  $ git revert commitID
```

##### 删除某次提交

```bash
  # 找到要回滚的 commitID
  $ git log --oneline -n5
  # 注意：最后的^号，意思是commit id的前一次提交
  $ git rebase -i "commit id"^ # git rebase -i "5b3ba7a"^

  # 根据交互输入命令修改信息
  # 合并多个历史版本commit合并
  $ git rebase -i [父级commit id ]

  # 合并多个历史版本commit合并
  $ git rebase -i [开始commit 的父级id] [结束commit id]
```

> 如果没有指定 结束 commit,那么结束 commit 默认为当前分支最新的 commit，那么 rebase 结束后会自动更新当前分支指向的 commit,
> 如果指定了结束 commit，而且结束 commit 不是当前分支最新的 commit，那么 rebase 后会有生成一个 游离的 head,，而且当前分支指向的 commit 不会更新

具体还可参考：
[Deleting a Git commit](https://www.jianshu.com/p/073acdc79c7b)
[在 Git 中，如何『删除』commit？](https://blog.csdn.net/u013553529/article/details/88087047)

### 操作标签

> 标签操作允许为存储库中的特定版本提供有意义的名称。

#### 创建标签

```bash
  # 创建标签
  $ git tag -a 'tag1' -m 'tag1的说明' [HEAD / commit id / 缺省]
  # -a选项的 标签名称，-m选项的 标签消息。
  # 缺省：HEAD 要标记特定提交，则使用相应的COMMIT ID而不是HEAD指针

  # 将 tag1 推送到 origin 远端
  $ git push origin <tagname>

  # 一次性推送全部尚未推送到远程的本地标签
  $ git push origin --tags
```

#### 查看标签

```bash
  $ git tag
  # 查看 所有可用的标签
  $ git tag -l

  # 查看 tagName标签的 详细信息
  $ git show tagName
```

#### 删除标签

```bash
  # 查看 所有可用的标签
  $ git tag -l

  # 删除 tagName标签
  $ git tag -d <tagName>

  # 删除 远端 tag 标签
  $ git push origin --delete tag <tagname>

  # 删除 远端 tag 标签 类似 删除远端分支的做法
  $ git push origin :refs/tags/标签名
```

### 操作分支

### 合并分支

## 常用命令详解

### git pull 详解

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

### git fetch 详解

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

### git add 详解

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

### git commit 详解

> 用于将更改记录(提交)到存储库

```bash
  # 普通提交填写提交信息
  $ git commit -m "the commit message"

  # 将所有已跟踪文件中的执行修改或删除操作的文件都提交到本地仓库，即使它们没有经过git add添加到暂存区，
  # 然后提交(有点像svn的一次提交,不用先暂存)。
  # 对于没有track的文件,还是需要执行`git add <file>` 命令。
  $ git commit -a
  $ git commit -a -m "the commit message"

  # 增补提交（修改上次提交），会使用与当前提交节点相同的父节点进行一次新的提交，旧的提交将会被取消。
  $ git commit --amend



```

### git remote 详解

> git remote 命令管理一组跟踪的存储库

```bash
# 查看当前的远程库
# 列出已经存在的远程分支
$ git remote
#  列出详细信息，在每一个名字后面列出其远程url
$ git remote [-v | --verbose]

# 添加一个名字为 <name> 的 远程服务器
# $ git remote add <name> <url>
$ git remote add [-t <branch>] [-m <master>] [-f] [--[no-]tags] [--mirror=<fetch|push>] <name> <url>
# 重命名 远程链接
$ git remote rename <old> <new>
# 删除 远程链接
$ git remote remove <name>


$ git remote set-head <name> (-a | --auto | -d | --delete | <branch>)
$ git remote set-branches [--add] <name> <branch>…​

# 获取 <name> 远程服务 地址
$ git remote get-url [--push] [--all] <name>
# 设置 <name> 远程服务 地址
$ git remote set-url [--push] <name> <newurl> [<oldurl>]
$ git remote set-url --add [--push] <name> <newurl>
$ git remote set-url --delete [--push] <name> <url>
$ git remote [-v | --verbose] show [-n] <name>…​
$ git remote prune [-n | --dry-run] <name>…​
$ git remote [-v | --verbose] update [-p | --prune] [(<group> | <remote>)…​]


```

### git push 详解

> 将修改添加到暂存区

#### 语法

```bash
  # 命令用于将本地分支的更新，推送到远程主机。与git pull命令相似。
  $ git push <远程主机名/缺省> <本地分支名/缺省>:<远程分支名/缺省>

```

#### 示例

> origin 可通过 .git config 查看地址

##### 完整示例

```bash
  # 将本地的master分支推送到origin主机的master分支。如果master不存在，则会被新建。
  $ git push origin master

```

##### 本地分支名 缺省

表示**删除指定的远程分支**，因为这等同于推送一个空的本地分支到远程分支。

```bash
  # 表示删除origin主机的master分支
  $ git push origin :master
  # 等同于
  $ git push origin --delete master
```

##### 本地分支、远程分支都 缺省

表示推送**特定主机的对应分支**简写方式：如果当前分支与远程分支之间存在追踪关系，则本地分支和远程分支都可以省略。

```bash
  # 将当前分支推送到origin主机的对应分支
  $ git push origin
```

##### 远程主机名、本地分支、远程分支都 缺省

表示推送**origin 主机的对应分支**简写方式：如果当前分支只有一个追踪分支，那么主机名都可以省略。

```bash
  # 将当前分支推送到 预设 主机【默认origin】的对应分支
  $ git push

```

当前分支与多个主机存在追踪关系，则可以使用-u 选项指定一个默认主机，这样后面就可以不加任何参数使用 git push

```bash
  # 将本地的master分支推送到origin主机，同时指定origin为默认主机，后面就可以不加任何参数使用git push了
  $ git push -u origin master
```

##### simple 方式 和 matching 方式

> 不带任何参数的 git push，默认只推送当前分支，这叫做 simple 方式
> matching 方式，会推送所有有对应的远程分支的本地分支
> Git 2.0 版本之前，默认采用 matching 方法，现在改为默认采用 simple 方式

如果要修改这个设置，可以采用 git config 命令

```bash
  $ git config --global push.default matching
  # 或者
  $ git config --global push.default simple
```

```bash
  # 不管是否存在对应的远程分支，将本地的所有分支都推送到远程主机
  # 所有本地分支都推送到origin主机
  $ git push --all origin

  # 使用-–force选项，结果导致在远程主机产生一个”非直进式”的合并(non-fast-forward merge)
  $ git push --force origin

  # git push不会推送标签(tag)，除非使用–tags选项
  $ git push origin --tags
  # 推送tag
  $ git push origin tag_name
  # 删除远程标签
  $ git push origin :tag_name

  # 将当前分支推送到远程的同名分支
  $ git push origin HEAD

  # 将当前所在指针分支 推送到 远程的master分支
  $ git push origin HEAD:master

  # 用本地分支 dev 覆盖远程分支 dev_op
  $ git push -f origin dev:refs/dev_op
  # or
  $ git push origin :refs/dev   //删除远程的dev分支
  $ git push origin dev:refs/dev_op

```

### git checkout 详解

> 用于切换分支或恢复工作树文件。这条命令会重写工作区

```bash
  # 切换 <branch> 分支
  $ git checkout <branch>
  # 从 <branch> 分支 提交中取出文件
  $ git checkout <branch>  <fileName>  
  # 从暂存区（index） 恢复文件
  $ git checkout <fileName>

  # 新建 < branch > 分支 并进行切换
  $ git checkout -b <branch>
  $ git checkout -b|-B <new_branch> [<start point>] # 完整版
  # 相当于 执行
  # git branch newBranch
  # git checkout newBranch

  # 换到newBranch的远程分
  $ git checkout -b newBranch  origin/newBranch


  # 检出索引中的所有C源文件
  $ git checkout -- '*.c'
  # 检出索引中的 fileName文件
  $ git checkout -- <fileName>

  # 检出索引中的 fileName文件
  $ git checkout -- <fileName>

  # 用于检出某一个指定文件
  # 不填写commit id，则默认会从暂存区检出该文件，如果暂存区为空，则该文件会回滚到最近一次的提交状态
  $ git checkout [-q] [<commit id>] [--] <paths>

  # 当暂存区为空，如果我们想要放弃对某一个文件的修改，可以用这个命令进行撤销
  $ git checkout [-q] [--] <paths>

  # 新建的分支，严格意义上说，还不是一个分支，因为HEAD指向的引用中没有commit值，只有在进行一次提交后，它才算得上真正的分支
  $ git checkout --orphan <new_branch>

  # 将当前分支修改的内容一起打包带走，同步到切换的分支下 [切换到新分支后，当前分支修改过的内容就丢失了]
  $ git checkout --merge <branch>
```

## git 原理

### git 工作解析图

下面这个图展示了工作区、版本库中的暂存区和版本库之间的关系：

![image](https://www.runoob.com/wp-content/uploads/2015/02/1352126739_7909.jpg)

### fast-forward [快进] 与 no-fast-forward

> 如果是正常 push 到仓库，正确的完成 commit 更新，那么这次更新就是一个 fast-forward 更新,而如果不理会错误警告用本地更新强制覆盖仓库，就是一次 no-fast-forward 更新，很明显，no-fast-forward 更新会导致记录丢失

具体阅读以下文章，感受下：

- [git：到底什么是 fast-forwards](https://www.jianshu.com/p/86c44bef4ea1)
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
