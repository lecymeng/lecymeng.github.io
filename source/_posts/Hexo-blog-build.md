---
title: HexoBlog搭建
date: 2015-11-24 12:18:46
updated: 2020-05-12 10:25:00
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
- 2）使用Homebrew 安装 Node.js (注意检查Node版本，截止2020.05.12 14.x版本 会导致hexo部署出错)

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
      branch: master	#部署分支
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
- 3）SSH无法连接到GitHub时，尝试 ssh-add ~/.ssh/私钥文件名
- 4）安装的时候最好加上sudo

## Hexo自动构建 By Travis
### 配置GitHub Token
如果需要使用travis自动化构建你的博客，travis自然需要读写你的GitHub上的Repo。GitHub提供了token机制来供外部访问你的仓库。

进入[github.com/settings/tokens](https://github.com/settings/tokens)，生成一个供travis读写你的GitHub用的token，至于token的权限，不会的直接全选了，但是不建议这样做，风险比较大，或者选择能够访问和提交仓库代码的权限即可，token注意保密，待会会用到。
![-w1028](https://blog-1251678165.cos.ap-chengdu.myqcloud.com/2020-05-12/15892123178972.jpg)

### 配置Travis-CI
使用GitHub账号登陆travis，在travis进入仓库同步管理进入travis-ci.org/profile，打开刚才托管的hexo博客源码仓库同步开关
![-w682](https://blog-1251678165.cos.ap-chengdu.myqcloud.com/2020-05-12/15892124687030.jpg)

进入设置页，设置自动化编译时机，自动化编译过程中需要用到的变量。
![-w1015](https://blog-1251678165.cos.ap-chengdu.myqcloud.com/2020-05-12/15892127916778.jpg)
![-w1171](https://blog-1251678165.cos.ap-chengdu.myqcloud.com/2020-05-12/15892128991659.jpg)

添加必要的Shell变量
![-w1475](https://blog-1251678165.cos.ap-chengdu.myqcloud.com/2020-05-12/15892129715179.jpg)
记住NAME 在配置travis.yml会用到

### 编写.travis.yml文件
.travis.yml是travis平台进行自动化构建的配置文件，travis会根据配置文件生成一个shell自动化脚本。

进入hexo博客源码本地repo

```bash
cd hexo
touch .travis.yml
vim .travis.yml
```

```yaml
# 指定构建环境是Node.js
language: node_js
# 指定版本，当前最新版14.x会导致hexo部署失败，改为12.16
node_js:
  - 12.16.3
# 指定需要sudo权限
sudo: required

# 设置缓存文件
cache:
  directories:
    - node_modules

# 设置钩子只检测hexo分支的push变动
branches:
  only:
    - hexo

# 在构建之前安装hexo环境
before_install:
  - npm install -g hexo-cli

# 安装git插件和搜索功能插件
install:
  - npm install
  - npm install hexo-deployer-git --save

# 执行清缓存，生成网页操作
script:
  - hexo clean
  - hexo generate

# 设置git提交名，邮箱；替换真实token到_config.yml文件，最后depoy部署
after_script:
  - git config user.name "lecymeng"
  - git config user.email "1570682285@qq.com"
  # 替换同目录下的 `_config.yml` 文件中 `github_token` 字符串为travis后台刚才配置的变量，注意>此处sed命令用了双引号，单引号无效
  - sed -i "s/github_token/${GITHUB_TOKEN}/g" ./_config.yml
  - sed -i "s/coding_token/${CODING_TOKEN}/g" ./_config.yml
  - sed -i "s/phone_number/${CODING_NUMBER}/g" ./_config.yml
  - hexo deploy
```

修改下_config.yml文件的deploy节点：
```yaml
# 修改前
deploy:
  - type: git
    repo: git@github.com:userName/RepoName.git
    branch: master
```

```yaml
deploy:
- type: git
  # github_token 会被 .travis.yml 中sed命令替换
  repo: https://github_token@github.com/lecymeng/lecymeng.github.io.git
  branch: master
- type: git
  # 新的e.coding 只能用电话和邮箱，不能使用账户名，邮箱带@字符会导致Git链接识别错误
  repo: https://phone_number:coding_token@e.coding.net/weicools/Weicools.git
  branch: master
```

由于部署机器上没有配置SSH，所以只能使用https协议，


### 配置参考
- https://michael728.github.io/2019/06/16/cicd-hexo-blog-travis/
- http://duansm.top/2018/08/05/hexo-travis/
- https://juejin.im/post/5a1fa30c6fb9a045263b5d2a
- https://github.com/xiong-it/xiong-it.github.io/blob/hexo/.travis.yml
- https://github.com/xiong-it/xiong-it.github.io/blob/hexo/_config.yml


## 双线部署 GitHub+Coding
hexo deploy 时需要使用https+TOKEN模式 实现更快部署
GitHub格式：https://github_token@github.com/useName/RepoName.git
Coding格式：https://[phone_number]:[coding_token/coding_password]@e.coding.net/useName/RepoName.git

### 开启Coding Page
打开仓库设置，打开持续集成和持续部署
![-w1760](https://blog-1251678165.cos.ap-chengdu.myqcloud.com/2020-05-12/15892135165321.jpg)
然后打开持续部署中的静态网站，点击立即部署，就会得到一个coding的博客网址
![-w1893](https://blog-1251678165.cos.ap-chengdu.myqcloud.com/2020-05-12/15892136176032.jpg)
然后点击设置，配置域名，配置之前需要先在域名解析中添加CNAME解析道 Coding的博客地址例如：[https://xxxx.coding-pages.com](https://030dsd.coding-pages.com)，注意如果有GitHub的解析的话需要先删除，否则无法开启https。
![141psU](https://blog-1251678165.cos.ap-chengdu.myqcloud.com/141psU.png)
配置好域名解析之后，在Coding中绑定域名，然后强制开启https，开启成功后再去配置GitHub的解析
![-w1578](https://blog-1251678165.cos.ap-chengdu.myqcloud.com/2020-05-12/15892138072661.jpg)

### 部署参考
- https://zhuanlan.zhihu.com/p/111608743?from_voters_page=true
- https://www.cnblogs.com/sunhang32/p/11969964.html
- https://huaien.co/technology/enable-https-on-coding-pages/

## 参考教程
- https://easyhexo.com/


