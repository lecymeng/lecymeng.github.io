---
title: HexoBlog搭建
date: 2015-11-24 12:18:46
updated: 2019-12-22 09:10:00
tags: [Hexo]
---

# HexoBlog搭建
更详细教程参考：https://easyhexo.com/

## 一.环境搭建
### 1. 安装git
    - Mac OSX下:安装Xcode即可/或者安装命令行工具
    - Windows下:安装git bash
<!--more-->

### 2. 安装node.js
- 1）Mac OSX下:首先安装Ruby环境，然后安装HomeBrew（详情Google）
    
    ```bash
    $ ruby -e "$(curl -fsSL https://raw.github.com/Homebrew/homebrew/go/install)”
    ```
- 2）使用Homebrew 安装 Node.js

    ```bash
    $ brew update
    $ brew install node
    ```
- 3）Windows/OSX下也可以进入[Node.js官网](https://nodejs.org/en/)下载安装包安装
- 4）一般 Node modules 通常被安装在每个项目的本地文件夹 node_modules，Npm 使用(更详细的命令操作参考百度/Google)

    ```bash
    $ npm install <package>     # 安装在本地项目中
    $ npm install -g <package>  # 安装在全局
    ```
    
## 二.安装Hexo（参考[Hexo](https://hexo.io/)）
### 1. 初始化和安装

在Finder中建立新的文件夹Hexo，cd进入这个目录下(推荐使用iTerm2)进行操作

    ```bash
    $ cd /Users/weico/Hexo	#这是我的电脑
    $ npm install hexo-cli -g
    $ hexo init blog
    $ cd blog
    $ npm install
    $ hexo g    #生成静态博客
    $ hexo s		#也可以指定端口$ hexo server -p 5000
    ```
### 2. Hexo简单使用
- 1）常用命令
    
    ```bash
    $ hexo new "postName" #新建文章
    $ hexo new page "pageName" #新建页面
    $ hexo generate #生成静态页面至public目录
    $ hexo server #开启预览访问端口（默认端口4000，'ctrl + c'关闭server）
    $ hexo deploy #将.deploy目录部署到GitHub
    $ hexo help  # 查看帮助
    $ hexo version  #查看Hexo的版本
    ```

- 2）复合命令
    
    ```bash
    $ hexo deploy -g  #生成加部署
    $ hexo server -g  #生成加预览
    $ hexo cl && hexo g && hexo d && hexo cl
    ```
- 3）命令简写
    
    ```bash
    $ hexo n == hexo new
    $ hexo g == hexo generate
    $ hexo s == hexo server
    $ hexo d == hexo deploy
    ```
### 3. 部署到GitHub
- 1）首先设置你的用户名密码
    
    ```bash
    $ git config --global user.email "bu.ru@qq.com"
    $ git config --global user.name "bruce-sha"
    ```
    
- 2）生成密钥
    
    ```bash
    $ ssh-keygen -t rsa -C "bu.ru@qq.com"
    ```
- 3）到/Users/用户名/.ssh下打开.pub文件将其添加到Github上的SSH Key
- 4）执行npm install hexo-deployer-git –save ## 安装部署所需要的插件
- 5）部署到Github前需要配置_config.yml文件，首先找到下面的内容
    
    ```yaml
    # Deployment
    ## Docs: http://hexo.io/docs/deployment.html
    deploy:
      type:
      
    # 将上面内容修改为：
    # Deployment
    ## Docs: http://hexo.io/docs/deployment.html
    deploy:
      type: git	#这里使用git，而不使用github
      repository: git@github.com:lecymeng/lecymeng.github.io.git	#这是我的Github账户
      branch: master	#分支
    ```

### 4. 插件安装
- 1）sitemap插件安装、使用-可以将你站点地图提交给搜索引擎，文件路径\sitemap.xml
    
    ```bash
    $ npm install hexo-generator-sitemap
    ```
    
    ```
    # Extensions
    Plugins:
    - hexo-generator-sitemap
    
    #sitemap
    sitemap:
      path: sitemap.xml
    ```

- 2）feed插件安装、使用-RSS的生成插件，你可以在配置显示你站点的RSS，文件路径\atom.xml
    
    ```bash
    $ npm install hexo-generator-feed
    ```
    
    ```
    # Extensions
    Plugins:
    - hexo-generator-feed
    - hexo-generator-sitemap
    #Feed Atom
    feed:
      type: atom
      path: atom.xml
      limit: 20
    ```
### 5. 公益404页面
- 腾讯公益404页面-在主题的source目录下创建404.html，内容如下
    
    ```html
    <!DOCTYPE HTML>
    <html>
    <head>
      <meta http-equiv="content-type" content="text/html;charset=utf-8;"/>
      <meta http-equiv="X-UA-Compatible" content="IE=edge,chrome=1" />
      <meta name="robots" content="all" />
      <meta name="robots" content="index,follow"/>
    </head>
    <body>
    
    <script type="text/javascript" src="http://www.qq.com/404/search_children.js" charset="utf-8" homePageUrl="your site url " homePageName="回到我的主页"></script>
    
    </body>
    </html>
    ```
    
### 6. 主体选择和配置
GitHub或者在官网可以找到很多很漂亮的主题，安装对应的文档进行配置即可

也可以参考 https://easyhexo.com/ 里面所罗列到的主题，都是非常不错的

### 7. Q & A
- 1）hexo ERROR Deployer not found: github，不能部署到github。
    
    ```
    解决办法：
    1.安装 npm install hexo-deployer-git –save；
    2.将Hexo的配置文件_config.yml中的deploy 的 type由github改为git
    ```

- 2）生成SSH key时，在那个目录下执行的ssh-keygen -t rsa -C “xxxx@xxxx.com”就会在那生成.pub文件，并不是在.ssh里面QAQ
- 3）安装的时候最好加上sudo

## 参考教程
- https://easyhexo.com/


