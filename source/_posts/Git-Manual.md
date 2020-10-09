---
title: Git使用手册
date: 2018-01-12 14:16:27
updated: 2019-11-25 16:40:00
tags: [Git, GitHub]
---

# Git使用手册

## Git配置
### 配置Git Config
```bash
# 配置UserName 和 Email
$ git config --global user.name "lecymeng"
$ git config --global user.email "1570682285@qq.com"

$ mkdir .ssh
$ cd ~/.ssh

# 生成 RSA Key
$ ssh-keygen -t rsa -C "1570682285@qq.com"
Generating public/private rsa key pair.
Enter file in which to save the key (/Users/weicools/.ssh/id_rsa): WeicoolsGitHub
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in WeicoolsGitHub.
Your public key has been saved in WeicoolsGitHub.pub.

weicools$ cat ~/.ssh/WeicoolsGitHub.pub
xxx...xxx 1570682285@qq.com
```

复制WeicoolsGitHub.pub内容到Git上的SSH key配置中
![](https://blog-1251678165.cos.ap-chengdu.myqcloud.com/vasIWL.png)

### 检查Git Config
```bash
$ git config --list
$ git config user.name
```

###  Git项目创建和提交
```bash
# 创建一个项目hello-world
$ mkdir ~/hello-world    

# 进入这个项目
$ cd ~/hello-world      

# 初始化Git
$ git init

# new README文件      
$ touch README

# add README文件
$ git add README

# 提交更新，并注释信息“first commit”
$ git commit -m 'first commit'

# 连接远程GitHub项目
$ git remote add origin git@github.com:xxx/yyyy.git

# 将本地项目更新到GitHub
$ git push -u origin master

# 后续提交 git push 即可
$ git push
```


## Git配置 Q & A
### set remote repository
```bash
$ git remote set-url origin https://github.com/Xxx/Yxx.git

$ git branch --set-upstream-to origin/master master
```

### add remote repository 
```bash
$ git remote add origin https://github.com/Xxx/Yxx
```

### Permission denied (手动add RSA密钥)
如果输入 `$ ssh -T git@github.com` 出现错误提示：Permission denied (publickey). 因为新生成的key不能加入ssh就会导致连接不上github。
解决办法：输入`$ ssh-add ~/.ssh/id_rsa`，再输入对应SSH密码即可

```bash
$ ls -ah
.      WeicoolsGitHub  config WeicoolsGitHub.pub  known_hosts

$ ssh-add ~/.ssh/WeicoolsGitHub

$ ssh -T git@github.com
Hi lecymeng! You've successfully authenticated, but GitHub does not provide shell access.
```


### 恢复删除分支
当我们使用git branch -D 删除一个本地分支后，怎样才能恢复呢？

```bash
$ 显示历史提交，找到分支对应的最后一笔提交的commit-id
git log -g

# 以指定commit为基础创建分支
$ git checkout -b {branch-name} {commit-id}
```

### ssh-add相关问题
[生成新 SSH 密钥并添加到 ssh-agent](https://help.github.com/cn/github/authenticating-to-github/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent)
[错误：权限被拒绝（公钥）](https://help.github.com/cn/github/authenticating-to-github/error-permission-denied-publickey)
[Git是否必须每次添加ssh-add](https://segmentfault.com/q/1010000000835302)
[配置多个ssh密钥对并且永久多ssh管理](https://www.yangyanxing.com/article/config-local-ssh.html)

## Fork项目
### 在GitHub上Fork
参考 https://github.com/selfteaching/the-craft-of-selfteaching/issues/67

### GitHub Fork实践
以 [material-components-android](https://github.com/material-components/material-components-android) 项目为例：

```bash
# Clone远程仓库
$ git clone git@github.com:lecymeng/material-components-android.git

# 查看远程仓库的路径
$ git remote -v
origin	git@github.com:lecymeng/material-components-android.git (fetch)
origin	git@github.com:lecymeng/material-components-android.git (push)

# 设置Repository upstream
$ git remote add upstream git@github.com:material-components/material-components-android.git

# 查看远程仓库的路径
$ git remote -v
origin	git@github.com:lecymeng/material-components-android.git (fetch)
origin	git@github.com:lecymeng/material-components-android.git (push)
upstream	git@github.com:material-components/material-components-android.git (fetch)
upstream	git@github.com:material-components/material-components-android.git (push)

# 抓取原仓库的更新
git fetch upstream

# 执行命令 git checkout master 切换到 master 分支
git checkout master

# 合并远程的master分支
git merge upstream/master 
```

## 使用SubModule
> 参考：
> imtianx: [Git 子模块](https://juejin.im/post/5aa11f486fb9a028e0140e34)
> 官方文档：[Git 工具 - 子模块](https://git-scm.com/book/zh/v2/Git-%E5%B7%A5%E5%85%B7-%E5%AD%90%E6%A8%A1%E5%9D%97)

工作中，可能会遇到**在一个Git仓库 中添加 其他 Git 仓库的场景**。比如，在项目中引用第三方库。或者在模块化开发中，某些公共的模块是需要单独维护的，使用单独的仓库比较方便，但是在项目中需要引用，就会出现这样的场景。这里使用 Git 的 `git submodule` 命令为一个 `git 项目` 添加 `子git项目`

可以使用 `git submodule --help` 查看所有相关命令。 为了方便说明，这里在主项目 `MainProject` 中加两个子模块 `liba` 和 `libb` .

### 1. 添加子模块
进入 `MainProject` 使用 **git submodule add** 进行添加,操作命令：

```bash
$ git clone https://github.com/imtianx/MainProject.git
cd MainProject/
$ git submodule add https://github.com/imtianx/liba.git
```

如下图： 
![](https://blog-1251678165.cos.ap-chengdu.myqcloud.com/2019-03-14-071321.jpg)

使用 `git submodule add https://github.com/imtianx/libb.git` 添加 `libb` 子模块。 对于上图: 文件夹 `liba` 为新增加的子模块目录, `.gitmodules` 中存放的为子模块的信息，使用 `cat` 或 `vim` 查看内容为：

```
[submodule "liba"]
	path = liba
	url = https://github.com/imtianx/liba.git
[submodule "libb"]
	path = libb
	url = https://github.com/imtianx/libb.git
```

> **.gitmodules文件**：保存项目 URL 与已经拉取的本地目录之间的映射，有多个子模块则含有多条记录，会随着版本控制一起被拉去和推送的。

此时文件目录树如下：

```
.
├── README.md
├── liba
│   ├── README.md
│   ├── a.txt
│   └── a2.txt
├── libb
│   ├── README.md
│   ├── b.txt
│   └── b2.txt
└── test.text
```

最后，**提交添加的子模块到主目录**

```bash
$ git commit -m "add liba and libb submodules"
[master 6b15e30] add liba and libb submodules
 3 files changed, 8 insertions(+)
 create mode 100644 .gitmodules
 create mode 160000 liba
 create mode 160000 libb
```

### 2. 更新子模块
往往子模块是单独开发的，这里以更新 `liba` 为例（为了测试，这里先在liba仓库添加了一个文件）：

```bash
$ cd liba
$ git fetch
$ git merge origin/master
```

操作结果如下图，**注意需要进入子模块目录**： 
![](https://blog-1251678165.cos.ap-chengdu.myqcloud.com/2019-03-14-071321.jpg)

此外，还可以在主目录下 直接用下面的命令更新 `libb`子模块：
```bash
git submodule update --remote liba
```

**使用下面的方式，更新 libb 的 dev 分支：**
```bash
$ git config -f .gitmodules submodule.libb.branch dev
$ git submodule update --remote
```

如下图： 
![](https://blog-1251678165.cos.ap-chengdu.myqcloud.com/2019-03-14-071531.jpg)

> 这里对 `.gitmodules` 加了 `-f` 参数，修改提交后对所有用户有效。

### 3. 删除子模块
在日常开发中，有添加，当然也会有删除 子模块的需求。 这里主项目包含两个子模块：`liba`、`libb`，以删除 `liba` 为例说明：

- 使用 `git rm --cached liba` 将liba 从版本控制中删除（本地仍保留有），若不需要可不带 `--cached`进行完全删除。
- 使用 `vim .gitmodules` 可打开vim编辑,删除对应的内容

```
 [submodule "liba"]
    path = liba
    url = https://github.com/imtianx/liba.git
    branch = dev
```

- 使用 `vim .git/config` 可打开vim编辑,删除对应的内容

```
[submodule "liba"]
    url = https://github.com/imtianx/liba.git
    active = true
```

- 使用 `rm -rf .git/modules/liba`, 删除.git下的缓存模块，最后提交项目。

经过上面的删除后还可以进行添加子模块。

### 4. 克隆含子模块的仓库
若需要克隆含有子模块的仓库，直接 进行克隆是无法拉取之模块的代码，可加上 **--recursive** 参数，如下：
```bash
git clone --recursive https://github.com/imtianx/MainProject.git
```

或者使用下面的三部操作：
```bash
git clone  https://github.com/imtianx/MainProject.git
git submodule init
git submodule update
```


## Git 分支开发规范
> 参考内容
> [Git分支管理策略](http://www.ruanyifeng.com/blog/2012/07/git.html)
> [您必须知道的 Git 分支开发规范](https://juejin.im/post/5b4328bbf265da0fa21a6820)
> [如何写好 Git commit messages](https://www.zhihu.com/question/21209619)
> [git commit 规范指南](https://segmentfault.com/a/1190000009048911)
> [Which characters are illegal within a branch name?](https://stackoverflow.com/questions/3651860/which-characters-are-illegal-within-a-branch-name)

Git 是目前最流行的源代码管理工具。 为规范开发，保持代码提交记录以及 git 分支结构清晰，方便后续维护，现规范 git 的相关操作。

![](https://blog-1251678165.cos.ap-chengdu.myqcloud.com/PWAUoF.jpg)


### 分支管理
#### 分支命名
**master 分支**
- master 为主分支，也是用于部署生产环境的分支，确保master分支稳定性
- master 分支一般由develop以及hotfix分支合并，任何时间都不能直接修改代码

**develop 分支**
- develop 为开发分支，始终保持最新完成以及bug修复后的代码
- 一般开发的新功能时，feature分支都是基于develop分支下创建的

**feature 分支**
- 开发新功能时，以develop为基础创建feature分支
- 分支命名: feature/ 开头的为特性分支， 命名规则: feature/user_module、 feature/cart_module

**release分支**
- release 为预上线分支，发布提测阶段，会release分支代码为基准提测

```
当有一组feature开发完成，首先会合并到develop分支，进入提测时，会创建release分支。
如果测试过程中若存在bug需要修复，则直接由开发者在release分支修复并提交。
当测试完成之后，合并release分支到master和develop分支，此时master为最新代码，用作上线。
复制代码
```

**hotfix 分支**
- 分支命名: hotfix/ 开头的为修复分支，它的命名规则与 feature 分支类似
- 线上出现紧急问题时，需要及时修复，以master分支为基线，创建hotfix分支，修复完成后，需要合并到master分支和develop分支

#### 常见任务
**增加新功能**

```bash
(dev)$: git checkout -b feature/xxx            # 从dev建立特性分支
(feature/xxx)$: blabla                         # 开发
(feature/xxx)$: git add xxx
(feature/xxx)$: git commit -m 'commit comment'
(dev)$: git merge feature/xxx --no-ff          # 把特性分支合并到dev
复制代码
```

**修复紧急bug**

```bash
(master)$: git checkout -b hotfix/xxx         # 从master建立hotfix分支
(hotfix/xxx)$: blabla                         # 开发
(hotfix/xxx)$: git add xxx
(hotfix/xxx)$: git commit -m 'commit comment'
(master)$: git merge hotfix/xxx --no-ff       # 把hotfix分支合并到master，并上线到生产环境
(dev)$: git merge hotfix/xxx --no-ff          # 把hotfix分支合并到dev，同步代码
复制代码
```

**测试环境代码**

```bash
(release)$: git merge dev --no-ff             # 把dev分支合并到release，然后在测试环境拉取并测试
复制代码
```

**生产环境上线**

```bash
(master)$: git merge release --no-ff          # 把release测试好的代码合并到master，运维人员操作
(master)$: git tag -a v0.1 -m '部署包版本名'  #给版本命名，打Tag
复制代码
```

![](https://blog-1251678165.cos.ap-chengdu.myqcloud.com/9czDAj.jpg)


### 日志规范
> 在一个团队协作的项目中，开发人员需要经常提交一些代码去修复bug或者实现新的feature。而项目中的文件和实现什么功能、解决什么问题都会渐渐淡忘，最后需要浪费时间去阅读代码。但是好的日志规范commit messages编写有帮助到我们，它也反映了一个开发人员是否是良好的协作者。

**编写良好的Commit messages可以达到3个重要的目的：**

- 加快review的流程
- 帮助我们编写良好的版本发布日志
- 让之后的维护者了解代码里出现特定变化和feature被添加的原因

**目前，社区有多种 Commit message 的写法规范。来自Angular 规范是目前使用最广的写法，比较合理和系统化。如下图：** 

![](https://blog-1251678165.cos.ap-chengdu.myqcloud.com/UlbImQ.jpg)

#### Commit messages的基本语法
当前业界应用的比较广泛的是 [Angular Git Commit Guidelines](https://docs.google.com/document/d/1QrDFcIiPjSLDn3EL15IJygNPiHORgU1_OOAqWjiDU5Y/edit#heading=h.greljkmo14y0)

具体格式为:

```
<type>: <subject>
<BLANK LINE>
<body>
<BLANK LINE>
<footer>
```

- type: 本次 commit 的类型，诸如 bugfix docs style 等
- scope: 本次 commit 波及的范围
- subject: 简明扼要的阐述下本次 commit 的主旨，在原文中特意强调了几点 1. 使用祈使句，是不是很熟悉又陌生的一个词，来传送门在此 祈使句 2. 首字母不要大写 3. 结尾无需添加标点
- body: 同样使用祈使句，在主体内容中我们需要把本次 commit 详细的描述一下，比如此次变更的动机，如需换行，则使用 |
- footer: 描述下与之关联的 issue 或 break change，详见案例

**Type的类别说明：**

- feature: 添加新特性/新功能
- upgrade: 功能升级或代码变更
- fix: 修复bug
- docs: 仅仅修改了文档或README
- style: 仅仅修改了空格、格式缩进、或者主题UI变更 等等，不改变代码逻辑
- refactor: 代码重构，没有加新功能或者修复bug
- perf: 增加代码进行性能测试
- test: 增加测试用例
- chore: 改变构建流程、或者增加依赖库、工具等

#### Commit messages格式要求
对于Git Commit Message并明确的标准，但也有一些好的原则：

* 使提交信息业务相关
* 提交信息中写明类型
* 必要时要写描述(Decription)
* 提交主题尽量简短
* 尽量使用英文


```
# 标题行：50个字符以内，描述主要变更内容
#
# 主体内容：更详细的说明文本，建议72个字符以内。 需要描述的信息包括:
#
# * 为什么这个变更是必须的? 它可能是用来修复一个bug，增加一个feature，提升性能、可靠性、稳定性等等
# * 他如何解决这个问题? 具体描述解决问题的步骤
# * 是否存在副作用、风险? 
#
# 如果需要的化可以添加一个链接到issue地址或者其它文档
```

#### 参考
[Git 如何写出优雅的Commit Message](https://juejin.im/post/6847902225029660680)
[利用emoji让的 git commit 生动清晰起来](https://juejin.im/post/6844903721244033038)
[优雅的提交你的 Git Commit Message](https://juejin.im/post/6844903606815064077)

### 利用emoji让的 git commit 生动清晰起来
有一些多人合作的项目同事提了commit，你还需要花时间去看什么提交了什么，因为什么提交。
有了emoji之后，例如看到有条虫🐛 就知道他这次版本就是改了bug了。
非常方便快捷，话不多说，开始吧。

如何食用：commit 时填写以下图标对应代码即可，例如：  `:bug:` 解决下单问题。

🎨 :art:
```
改进代码结构或者格式。
```

⚡ :zap:
```
提高效率。
```

🔥 :fire:
```
删除代码或文件。
```

🐛 :bug:
```
修复bug。
```

🚑 :ambulance:
```
删除代码或文件。
```

🔥 :art:
```
删除代码或文件。
```

✨ :sparkles:
```
增加新功能。
```

📝 :memo:
```
书写文档，例如修改readme.md。
```

🚀 :rocket:
```
部署项目。
```

💄 :lipstick:
```
更新UI和样式文件，例如更新css（前端应该经常都要这个涂唇膏的）。
```

🎉 :tada:
```
首次提交 first commit。
```

✅ :white_check_mark:
```
添加测试。
```

🔒 :lock:
```
解决安全问题。
```

♻ :recycle:
```
重构代码。
```

➕ :heavy_plus_sign:
```
添加依赖。
```

➖ :heavy_minus_sign:
```
删除依赖。
```

🔧 :wrench:
```
更改配置文件。
```

💩 :hankey:
```
编写需要改进的错误代码（这个emoji也太真实了吧）。
```

➖ :heavy_minus_sign:
```
删除依赖。
```

http://emojihomepage.com/
https://gitmoji.carloscuesta.me/