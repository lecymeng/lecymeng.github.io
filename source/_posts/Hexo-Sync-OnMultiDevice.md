---
title: 比较合适的Hexo多设备同步方法
date: 2018-11-11 13:30:10
tags: [Hexo, GitHub]
---

# 比较合适的 Hexo 多设备同步方法

经常我们有一个场景：需要在公司或者家庭多个电脑完成 Hexo 的博客撰写和发布工作。这就涉及到 Hexo 多电脑的同步问题。

网上的方案基本上都是多分支方案，也就是在同一个仓库创建两个分支：

1. Hexo 分支 – 用来保存所有 Hexo 的源文件
2. master 分支 – 用来保存 Hexo 生成的博客文件

在创建 GitHub Pages 或者 Coding Pages 时，以 master 分支为 pages 分支。
Hexo 的 deploy 指向 master 分支部署 pages，git 的管理指向 Hexo 分支。

<!--more-->

因为我的 GitHub Pages 对应的仓库是 public 的，所以会导致 Hexo 源文件暴露在公开的仓库了。这样也就会把我的博客的一些密钥、统计管理配置等暴露在公开仓库分支了。如果对这些配置的`_config.yml`进行单独管理的话，也很麻烦。

所以**Hexo 最完美的多电脑同步方法**是，创建两个仓库：

1. Hexo 私有仓库 – 用来保存所有 Hexo 的源文件
2. master 公开仓库 – 用来保存 Hexo 生成的博客文件

## 我的 Blog 配置

1. 利用 hexo d 直接 deploy Hexo 博客到 GitHub pages
2. 使用 hexo-theme-material 主题，并作了很多配置

## 创建远程私有仓库

我是在 Coding 上创建的（如果在 GitHub 可以创建 Private 仓库也可以在 GitHub 上创建）名字就叫 blog。

## 建立本地 git 仓库

进入你现有的本地 hexo 博客文件夹，先删除第三方主题的 git 配置，如对`hexo-theme-material`主题

```bash
rm -fr ./themes/hexo-theme-material/.git/
```

然后建立本地的 git 仓库

```bash
git init
```

创建一个`.gitignore`文件，并放在 Hexo 的根目录，内容为：

```
.DS_Store
Thumbs.db
db.json
*.log
node_modules/
public/
.deploy*/
```

![](https://blog-1251678165.cos.ap-chengdu.myqcloud.com/2018-11-11-055806.png)

## Push 到私有仓库

```bash
git remote add origin https://git.coding.net/<yourname>/blog.git
git add .
git commit -m "init my private hexo blog"
git push -u origin master
```

至此，就完成了本地 Hexo 源码的全备份

## 在另一台电脑进行 Hexo 写作

首先和最开始使用 Hexo 一样，都有完成 git、node、hexo 等环境的搭建和配置

```bash
brew install git

brew install node

brew install npm

npm install hexo-cli -g
```

### 拉取 Hexo blog

```bash
git clone https://git.coding.net/<yourname>/blog.git
```

这样你就拥有了你的所有 Hexo 源文件

### Hexo 编写和发布

尽管拉取下来了，还需要建立一下 Hexo 的环境，这里需要格外注意的一点是：
千万不要用`hexo init`命令。原因是当前目录已经建立了 git 仓库环境, `hexo init`会覆盖到当前的 git 环境，重建一个新的，这样和我们的私有 Hexo 源码仓库脱离了联系。

正确的做法是：

```bash
sudo npm install
```

因为`package.json`里面已经保存了`hexo`的必备资源包信息，`npm install`后 Hexo 环境就建立起来了。

如果需要更新 Hexo 版本或者插件版本，直接使用`npm update`即可，然后就可以看到 package.json 里面升级了哪些插件。

```bash
sudo npm update
```

接下来就进行正常的编写和发布就好。本地预览的命令还是：

```bash
hexo g
hexo s
```

Hexo 的发布命令是`hexo d`

最后执行`git status`把更改的新文件`git add`和`git commit`，最后`git push`到私有仓库，又会完成 Hexo 源码仓库的同步。

## END

从此，世界是如此的美好。
