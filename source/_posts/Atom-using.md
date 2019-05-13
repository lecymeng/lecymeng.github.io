---
title: Atom使用体验
date: 2015-12-28 12:18:18
tags: [Atom]
---

### 一.Atom安装
1. 打开https://atom.io/ 下载Windows安装包安装即可
2. 这里是Atom1.9版本所有平台下载链接
<!--more-->
### 二.Atom主题安装
个人很喜欢aom-material-ui这个主题（Sublime也使用）：https://atom.io/themes/atom-material-ui - （A dark UI theme for Atom that follows Google's Material Design Guidelines）
1. 可以直接使用官方推荐的安装方式
  - apm install atom-material-ui
  - apm install atom-material-syntax
  - apm install atom-material-syntax-light
  - apm install atom-material-syntax-dark
2. 由于我在安装的时候node-gyp报错，所有我是先把主题下载到：C:\Users\lecym\.atom\packages（这是Window平台lecym是用户名，Mac下cd ~/.atom/packages即可）
  - 切换到C:\Users\lecym\.atom\packages，执行以下命令即可完成安装
  ```bash
  git clone https://github.com/atom-material/atom-material-ui
  cd <Package 路径> # cd emmet-atom
  npm install
  ```
  - 其余三个安装方式同理
  ```bash
  git clone https://github.com/atom-material/atom-material-syntax
  cd <Package 路径> # cd emmet-atom
  npm install
  ```
  ```bash
  git clone https://github.com/atom-material/atom-material-syntax-light
  cd <Package 路径> # cd emmet-atom
  npm install
  ```
  ```bash
  git clone https://github.com/atom-material/atom-material-syntax-dark
  cd <Package 路径> # cd emmet-atom
  npm install
  ```
3. 图图图图：
![light](http://ob9ev3u0o.bkt.clouddn.com/ATOM1.png)
![dark](http://ob9ev3u0o.bkt.clouddn.com/ATOM2.png)

### 三.Atom插件安装
1. Atom 自带了一个 apm 的 Package 管理工具-打开File/Setting/Package，可用直接使用自带的安装方式
2. 离线安装方式和安装主题方式类似，由于我使用自带安装方式安装报错(和安装主题时报错一样)：
  ```bash
  gyp info it worked if it ends with ok
  gyp info using node-gyp@2.0.2
  gyp info using node@0.10.40 | win32 | ia32
  gyp http GET https://atom.io/download/atom-shell/v0.37.8/node-v0.37.8.tar.gz
  gyp WARN install got an error, rolling back install
  gyp
  ```
  - 打开https://atom.io/packages/，搜索需要安装的插件

  - 打开插件链接，里面有安装方法与主题安装方式一模一样
  ```bash
  cd ~/.atom/packages
  git clone https://github.com/emmetio/emmet-atom
  cd emmet-atom
  npm install
  ```

3. 比较优秀的插件
  - autocomplete-plus：自动补全代码可选择各种语言
  - emmet：Web前端开发神器，它使用仿CSS选择器的语法来生成代码，大大提高了HTML/CSS代码编写的速度
  - linter-jshint：它有一个配置文件.jshintrc，这个文件告诉jshint执行的检查规则，通过jshint能发现代码中存在的问题，可以及时避免bug的发生
  - highlight-selected：顾名思义高亮当前所选文字，双击后全文这个词或者变量都会高亮
  - git-control：atom对git的支持还是不如完整IDE开发工具好用，不过插件还是能弥补这个问题。git-control能提供一个git gui来来完成git的版本管理，非常方便操作
  - highlight-line：对光标所在行以高亮显示，从而可以准确定位光标所在的行
  - docblockr：可以帮助你快速的生成注释，很多高级的编辑器都有这个功能，只是atom没有内置实现
  - simplified-chinese-menu：可以使用简体中文插件来汉化atom编辑器（基本也不用，就那么几个词也不难）
  - atom-beautify：代码格式化，可以对html、css、js等文件进行格式美化，使用时需要先选中要美化的代码
  - atom-ternjs：能对一个对象中拥有的对外提供的属性和方法都能通过suggest的形式提示出来，能对一个对象对外提供的接口有一个选择过程
4. 好玩的插件
  - activate-power-mode：给写代码带来的超爽视觉震撼
  ![atom-code1](http://ob9ev3u0o.bkt.clouddn.com/atom-code1.gif)
  ![atom-code2](http://ob9ev3u0o.bkt.clouddn.com/atom-code2.gif)
  - atom-miku：让初音未来歌姬来鼓励你撸代码，Miku能从你的代码中得到获得充能！
  ![atom-miku](http://ob9ev3u0o.bkt.clouddn.com/atom-miku.gif)

### 四.Atom体验
1. Sublime 是原生界面，脚本用的是 python；
2. Atom 应该是基于 Chromium Embedded Framework，基本上就是个 web app，源码都是 CoffeeScript 写的，连界面都可以用 CSS 来自定义
3. 除了基本的操作和界面外，和 Sublime 最大的差别在于扩展性。Atom 非常强调模块化，很多默认功能也都是开源的模块。自带友好的模块管理界面，相比之下 Sublime 需要自己手动安装，或是依赖第三方的 package control
4. Atom 的扩展也是用 JS 或者 Coffee 在 Node + webkit 的环境下开发，并且可以使用 npm 的包，这对于前端和 Node 开发者是很有诱惑力的，需要的话完全可以把 Atom 打造成一个 IDE
5. 一个明显的缺点是，启动和开文件速度明显不如 Sublime 3，因为Atom比较大

