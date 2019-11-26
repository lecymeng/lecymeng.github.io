---
title: 注入EDID
date: 2017-08-18 14:16:27
tags: [macSierra, EDID]
---

1. 打开DarwinDumper.app，只选择EDID然后点击run，默认会在`/Applications/DarwinDumperReports/`目录下生成，比如：`/Applications/DarwinDumperReports/000_2017-08-23_10-44-07_MacBookPro14,3/DarwinDumper_2.8.8_INSYDE_Corp._X64_Unknown_weicools/EDID`
    ![DarwinDumper](https://blog-bak-1251678165.cos.ap-beijing.myqcloud.com/2017-08-23-034014.jpg)
    ![DarwinDumperReports](https://blog-bak-1251678165.cos.ap-beijing.myqcloud.com/2017-08-23-034403.jpg)
<!--more-->
2. 把生成的EDID考到桌面文件夹，打开FixEDID.app（2.3版本），选择EDID.hex文件（不知道为啥按照这个教程[[分享] 轻松注入EDID参数](http://bbs.pcbeta.com/viewthread-1473634-1-1.html)其中说了：***2、打开FixEDID软件，在主界面会有一个open edid binary file按钮，点击该按钮，然后选中上一步生成的EDID.hex文件( 最新版选择的文件是二进制文件EDID.bin)***但是我用2.3版本选择bin文件报错，只能选hex文件）
    ![FixEDID.app2.3](https://blog-bak-1251678165.cos.ap-beijing.myqcloud.com/2017-08-23-190209.jpg)
    点击make后，桌面上会生成这三个东西：
    ![DisplayVendorID-30e4](https://blog-bak-1251678165.cos.ap-beijing.myqcloud.com/2017-08-23-190427.jpg)

3. 然后呢？？？（我安装DisplayMergeNub.kext没有效果，然后再把DisplayVendorID-30e4文件夹扔到`/System/Library/Displays/Contents/Resources/Overrides` 就直接开机黑屏了）到达该咋办？？？


按照大佬ed流程：-注入EDID，-显示器内建，-IORegistryExplorer软件看到AppleBacklightDisplay被加载，-PNLF代码 ，-在PEG0 的PEGP之下添加。

注入EDID我是按教程这样弄得，但是弄到第三步不知道怎么弄才可以了
1. 打开DarwinDumper.app，只选择EDID然后点击run
[img=664,575]https://blog-bak-1251678165.cos.ap-beijing.myqcloud.com/2017-08-23-034014.jpg[/img]
[img=1179,436]https://blog-bak-1251678165.cos.ap-beijing.myqcloud.com/2017-08-23-034403.jpg[/img]

2.把生成的EDID考到桌面文件夹，打开FixEDID.app（2.3版本），选择EDID.hex文件
[img=1463,529]https://blog-bak-1251678165.cos.ap-beijing.myqcloud.com/2017-08-23-190209.jpg[/img]
点击make后，桌面上会生成这三个东西：
[img=770,436]https://blog-bak-1251678165.cos.ap-beijing.myqcloud.com/2017-08-23-190427.jpg[/img]

3.然后呢？？？（我安装DisplayMergeNub.kext没有效果，然后再把DisplayVendorID-30e4文件夹扔到/System/Library/Displays/Contents/Resources/Overrides 就直接开机黑屏了）该咋办？？？

PLNF代码没加，PEGP代码已注入，IORegistryExplorer中没看到AppleBacklightDisplay加载，显示器还没內建，已经替换了10.12.4驱动，目前情况就是这样{:5_275:}。求大佬解答帮帮忙



更新了DarwinDumper版本到3.0.3生成的EDID bin文件在FixEDID 2.3中打开不会报错了，但是把里面所有显示器型号都试了一遍，生成的驱动安装都是签名有问题[img=1091,902]https://blog-bak-1251678165.cos.ap-beijing.myqcloud.com/2017-08-26-134239.jpg[/img]

无奈只能把DisplayVendorID-30e4放到display中的Overrides里面，但是也是只有MBP这个型号可以用其他基本都是开机黑屏。。。效果是这样的：我17.3显示器显示成了13.3分辨率显示也不一样[img=1463,530]https://blog-bak-1251678165.cos.ap-beijing.myqcloud.com/2017-08-26-155155.jpg[/img]

试了一晚上了{:5_275:}，显示器內建只是注入EDID？还要怎么操作？？？





00FFFFFF FFFFFF00 30E46E04 00000000 00180104 A5261578 0A0BB5A3 5955A027 0C505400 00000101 01010101 01010101 01010101 01012E36 80A07038 1F403020 35007ED7 1000001B 1F2480A0 70381F40 30203500 7ED71000 001B0000 00000000 00000000 00000000 00000000 00000002 000A30FF 0A3C9619 1D489600 0000003F 

00ffffff ffffff00 30e46e04 00000000 00180104 a5261578 0a0bb5a3 5955a027 0c505400 00000101 01010101 01010101 01010101 01012e36 80a07038 1f403020 35007ed7 1000001b 1f2480a0 70381f40 30203500 7ed71000 001b0000 00000000 00000000 00000000 00000000 00000002 000a30ff 0a3c9619 1d489600 0000003f

修改DSDT的方式，我不太清楚。再提一下注入EDID/显示器內建过程，我发现并不需要DarwinDumper和FixEDID这两个工具来完成。原因如下：使用FixEDID 2.3选择EDID.bin文件后选择相应显示器等等一系列操作 ，生成的文件夹里面数据不对（生成的驱动在我这里安装后显示签名错误，不能使用）。所有直接用IORegistryExplorer查看相关数据填上去就OK啦~亲测成功，上图：
[img=1471,977]https://blog-bak-1251678165.cos.ap-beijing.myqcloud.com/2017-08-27-EDID.png[/img]
关于IODisplayEDID的Data也可以不用一个一个的填上去，可以按下图方式：使用PlistEdit Pro打开DarwinDumper生成的EDID.hex，具体操作如下：
[img=1598,976]https://blog-bak-1251678165.cos.ap-beijing.myqcloud.com/2017-08-27-EDID%20Data.png[/img]

附上可参考的文件模板：[attach]3991721[/attach]照着改就好了，改完记得把文件夹扔到/System/Library/Displays/Contents/Resources/Overrides目录下

效果展示：
[img=1091,926]https://blog-bak-1251678165.cos.ap-beijing.myqcloud.com/2017-08-27-181802.jpg[/img]
[img=1092,927]https://blog-bak-1251678165.cos.ap-beijing.myqcloud.com/2017-08-27-181851.jpg[/img]
显示器就不会是原来显示的那样了，13.3寸一看就是数据不对{:5_260:}，改了之后就正常了{:5_262:}



上一篇帖子：
[分享] 暗影精灵2Plus GTX1060独显亮度调节实现（http://bbs.pcbeta.com/viewthread-1755616-1-1.html）

关于修改DSDT的方式，我不太清楚。我自己尝试了注入EDID/显示器內建过程，我发现并不需要DarwinDumper和FixEDID这两个工具来完成。原因如下：使用FixEDID 2.3选择EDID.bin文件后选择相应显示器等等一系列操作 ，生成的文件夹里面数据不对，虽然还是能够看到显示器內建了但是看着不爽啊（生成的驱动在我这里安装后显示签名错误，不能使用）。所有直接用IORegistryExplorer查看相关数据填上去就OK啦~亲测成功，上图：
[img=1471,977]https://blog-bak-1251678165.cos.ap-beijing.myqcloud.com/2017-08-27-EDID.png[/img]
关于IODisplayEDID的Data也可以不用一个一个的填上去，可以按下图方式：使用PlistEdit Pro打开DarwinDumper生成的EDID.hex，具体操作如下：
[img=1598,976]https://blog-bak-1251678165.cos.ap-beijing.myqcloud.com/2017-08-27-EDID%20Data.png[/img]

附上可参考的文件模板：[attach]3991721[/attach]照着改就好了，改完记得把文件夹扔到/System/Library/Displays/Contents/Resources/Overrides目录下
附上度盘：链接: https://pan.baidu.com/s/1kViOmwv 密码: 7axt

效果展示：
原来是：

改之后：
[img=1091,926]https://blog-bak-1251678165.cos.ap-beijing.myqcloud.com/2017-08-27-181802.jpg[/img]
[img=1092,927]https://blog-bak-1251678165.cos.ap-beijing.myqcloud.com/2017-08-27-181851.jpg[/img]
显示器就不会是原来显示的那样了，13.3寸一看就是数据不对{:5_260:}，改了之后就正常了{:5_262:}

