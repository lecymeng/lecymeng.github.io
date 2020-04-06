---
title: 制作多功能启动U盘, 轻松完成系统安装和维护
date: 2017-12-02 11:16:27
tags: [macOS, Linux, Windows]
---

# 制作多功能启动U盘, 轻松完成系统安装和维护
> 本教程仅适用于EFI引导的电脑，安装的系统也需要支持EFI引导。

平时给自己或者别人重装系统的频率还算挺高的，因为有时候要装 macOS 有时候要装 Windows，每次都要重新把安装包刷到我的 U 盘里，不但非常折腾，而且对寿命也不好。于是整理了一下把安装盘都塞到一个 U 盘里，这个 U 盘不仅可以选择启动多个系统，而且当某一个系统的安装盘需要更新的时候，并不需要抹掉整个盘，只要单独处理对应的分区就好，非常方便。启动的时候效果是这样的：
<!--more-->
![引导画面](http://blog-1251678165.coscd.myqcloud.com/2017-12-03-MiPro%20Boot.jpg) 启动箱选择界面

    * Linpus lite (sanDisk) -- 引导试用/安装/启动 Ubuntu 16.04.3
    * EFI USB Device (SanDisk) -- 引导`macOS安装和启动` 以及 `Windows/Linux等支持EFI引导的系统启动`
    * EFI USB Device 1 (SanDisk) -- 引导Windows系统安装
    * EFI USB Device 2 (SanDisk) -- 引导进入WeiPE

这个 U 盘包括了一个 macOS 安装器、一个 Ubuntu 16.04.3 、一个 Windows10 安装器 和一个 WinPE。接下来的教程就是教大家怎样制作一个这样的启动 U 盘并应对一些启动问题。

## 材料准备
1. 至少16GB的 U 盘, 32GB 最佳
2. [macOS安装程序](https://itunes.apple.com/cn/app/macos-high-sierra/id1246284741?mt=12) (7GB++, 根据安装程序大小决定)
3. [Linux安装镜像](http://releases.ubuntu.com/16.04/) (2GB++, 根据安装镜像大小决定)
4. [Windows安装镜像](https://msdn.itellyou.cn/) (5GB++, 根据安装镜像大小决定)
5. [WeiPE生成的镜像](http://www.wepe.com.cn/download.html) (300MB++, 根据安装镜像大小决定)
6. 其他软件工具: 磁盘工具, [DiskGenius](http://www.zdfans.com/6959.html), [Unetbootin](http://unetbootin.github.io/), [CloverBootLoader](https://sourceforge.net/projects/cloverefiboot/?source=typ_redirect)

    * U盘建议使用USB 3.0的
    * macOS可以直接从AppStore下载原版安装文件
    * Linux可以去Ubuntu官网下载, 也可以使用其他发行版Linux(需要支持EFI)
    * Windows原版镜像可以从I tell you上获取
    * PE可以使用WeiPE或者AOMEI PE, 本教程使用WeiPE(PE需要的工具基本自带)

    ![](http://blog-1251678165.coscd.myqcloud.com/2017-12-02-162348.png)

## 制作 macOS 安装盘
1. 打开磁盘工具，首先在左边选中你的 U 盘，**注意是选中 U 盘而不是其中的分区**，之后点击抹掉按钮，这会把整个磁盘抹成 GPT。⚠️**该操作会清空 U 盘的全部内容，注意备份。**⚠️（虽然一些软件可以直接转换 MBR 和 GPT，不过我还是觉得直接都抹掉比较干净）

    按照下面的做法，将分区格式选为 Mac OS 扩展（日志式），方案选择 GUID 分区图。点击抹掉。有一定概率出现第一次抹掉不成功的情况，是由于系统没有正确卸载磁盘导致的，这个 Bug 很常见。如果第一次失败了，就再试一次基本上就没问题了。

    ![](http://blog-1251678165.coscd.myqcloud.com/2017-12-02-162915.png)
    ![](http://blog-1251678165.coscd.myqcloud.com/2017-12-02-162943.png)

2. 之后还是选中 U 盘，点击分区。你现在应该看到整个硬盘使用一个分区。点击下面的加号新建一个分区，上面的硬盘会被平均分为两个。选择右边的一个（第一个分区），在右侧的详细信息中将分区的名称改为「macOS」（在之后的写入命令中我们会用到这个名称），将大小改为 7.5GB（其实可以再小一点，但是为了能够兼容之后的 macOS 更新以及能够放一些常用程序，这里 7.5 是一个比较合理的大小）

    ![](http://blog-1251678165.coscd.myqcloud.com/2017-12-02-163036.png)
    
    ![](http://blog-1251678165.coscd.myqcloud.com/2017-12-02-163210.png)
    
    ![](http://blog-1251678165.coscd.myqcloud.com/2017-12-02-163239.png)

3. 用命令制作安装盘
    命令(具体可参照下图)：`sudo /Applications/Install\ macOS\ High\ Sierra.app/Contents/Resources/createinstallmedia  --volume /Volumes/macOS --applicationpath /Applications/Install\ macOS\ High\ Sierra.app --nointeraction`
    ![](http://blog-1251678165.coscd.myqcloud.com/2017-12-02-QQ20171203-003628.png)
    ![](http://blog-1251678165.coscd.myqcloud.com/2017-12-02-163919.png)
    ![](http://blog-1251678165.coscd.myqcloud.com/2017-12-02-164734.png)

4. 安装Clover引导
    ![](http://blog-1251678165.coscd.myqcloud.com/2017-12-02-165039.png)
    
    安装完成后放Kexts，主题，APFS驱动，配置config.plist......
        
5. 拷贝常用软件
    可以往Install macOS High Sierra安装盘放些驱动/常用App/工具之类的，安装完系统可以从U盘里面拷贝使用.

## 写入 Linux/Windows/PE 镜像
1. 进入Windows系统(也可以使用虚拟机，系统可能会提示分区损坏，取消掉不要格式化，这是因为 Windows 读不了 HFS+ 的分区), 准备好工具: [DiskGenius](http://www.zdfans.com/6959.html), [Unetbootin](http://unetbootin.github.io/), 镜像：[Linux安装镜像](http://releases.ubuntu.com/16.04/), [Windows安装镜像](https://msdn.itellyou.cn/), [WeiPE生成的镜像](http://www.wepe.com.cn/download.html)
2. 打开DiskGenius, 删除在macOS上分出来的区`Others`, 然后未分区的位置新建分区如下图：按照镜像大小new出三个FAT32分区，分别是LINUX, WIN10, WEIPE。(卷标自己命名，新建分区时以GB为单位只能新建出整数大小，可以在新建完这个分区后对其调整)
    ![](http://blog-1251678165.coscd.myqcloud.com/2017-12-03-u-disk-1-delete.png)
    ![](http://blog-1251678165.coscd.myqcloud.com/2017-12-03-u-disk-2-newLinuxPart.png)
    ![](http://blog-1251678165.coscd.myqcloud.com/2017-12-03-u-disk-3-chang-size.png)
    ![](http://blog-1251678165.coscd.myqcloud.com/2017-12-03-u-disk-4-newWinPart.png)
3. 新建完分区后。使用Unetbootin把对应的系统镜像写入到对应分区中：⚠️**千万不要选错，把别的分区抹掉就要重来了。**⚠️
    ![](http://blog-1251678165.coscd.myqcloud.com/2017-12-03-u-disk-5-copyUbuntu.png)
    ![](http://blog-1251678165.coscd.myqcloud.com/2017-12-03-u-disk-6-copyWin10.png)
    ![](http://blog-1251678165.coscd.myqcloud.com/2017-12-03-Windows10%20make.png)

    对于WEIPE，官网下载得到的是WeiPE.exe文件，打开并选择`生成ISO文件`，得到ISO文件后，直接`双击打开`，`全选里面的文件拷贝到PE分区` 或者 用`Unetbootin写入`也一样。
    ![](http://blog-1251678165.coscd.myqcloud.com/2017-12-03-u-disk-WEIPE-1.png)
    ![](http://blog-1251678165.coscd.myqcloud.com/2017-12-03-u-disk-WEIPE-2.png)
    ![](http://blog-1251678165.coscd.myqcloud.com/2017-12-03-u-disk-WEIPE-3.png)
4. 最终得到的效果如下(不要在意U盘和盘符，我用两个U盘分别做了一次，截图乱了点 ╮(￣▽￣)╭)，重启按F12(通常是F12)进入启动项选择界面即可选择对应的启动项。
    ![](http://blog-1251678165.coscd.myqcloud.com/2017-12-03-u-disk-newThreePart.png)
    ![](http://blog-1251678165.coscd.myqcloud.com/2017-12-03-u-disk-final.png)
    ![引导画面](http://blog-1251678165.coscd.myqcloud.com/2017-12-03-MiPro%20Boot.jpg)


## Q & A
1. 安装盘有了新版本怎么办？
    * 只要抹掉该分区，重新按照之前的步骤刷入即可。Windows 也是同理，不过要在 Windows 中就格式化对应分区重新写入。
2. 有安装盘的那些分区可以自己存文件么？
    * 当然可以，可用空间里面随便存，不会浪费。比如Install macOS High Sierra的盘可以放些驱动/常用App之类的，安装完系统可以从U盘里面拷贝使用，WIN10分区可以放些PE没有的一些工具，也可以存自己日常的一些文件，不过最好新建文件夹来存储。
    ![](http://blog-1251678165.coscd.myqcloud.com/2017-12-03-032556.png)
    ![](http://blog-1251678165.coscd.myqcloud.com/2017-12-03-032634.png)
3. 推出 U 盘的时候总是无法推出怎么办？
    * 因为四个区的关系，在较慢的 U 盘上推出的时候会非常的卡，这是正常现象，多等一会就好了。

## 参考资料
> - [制作一个四合一维护 U 盘，重装系统还能有多方便？](https://sspai.com/post/41960)
> - [计算机是如何启动的 - 阮一峰](http://www.ruanyifeng.com/blog/2013/02/booting.html)
> - [UEFI 和 BIOS 引导有什么不同 - 知乎](https://www.zhihu.com/question/21672895)
> - [Correct name and icons in Startup Manager ](https://decio.eu/2014/01/16/correct-name-and-icons-in-startup-manager/)


