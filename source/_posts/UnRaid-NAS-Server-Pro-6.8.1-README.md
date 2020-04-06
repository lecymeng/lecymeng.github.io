---
title: unRAID NAS Server Pro 6.8.1 快乐版
date: 2020-04-06 11:55:00
tags: [NAS, Unraid]
---

<!--[unRAID NAS Server Pro 6.8.2 开心版](http://www.hopol.cn/2020/01/1510/)-->

下载地址：https://mega.nz/folder/kv4w0YJA#YtT-Kr3lfa2zDPrEOpSEyw

## 解压后截图

![Crms8X](https://blog-1251678165.cos.ap-chengdu.myqcloud.com/Crms8X.jpg)

## 食用方式

将U盘格式化为FAT32格式，名称为：UNRAID

将解压出来的所有文件拷贝到U盘根目录下
![VQXhuT](https://blog-1251678165.cos.ap-chengdu.myqcloud.com/VQXhuT.jpg)

用管理员权限运行 make_bootable.bat

将U盘插入设备并启动，正常引导之后会看到当前获取到的 IP。
<!--more-->
我这里直接插入网线，上级有DHCP服务器，所以直接获取到局域网IP。

浏览器输入IP，默认打开的页面就能看到 Flash GUID
![QhfdHI](https://blog-1251678165.cos.ap-chengdu.myqcloud.com/QhfdHI.jpg)

格式为 XXXX-XXXX-XXXX-XXXXXXXXXXXX ,复制备用。

拔下引导U盘，插入PC端，输入如下：
![cnIXF1](https://blog-1251678165.cos.ap-chengdu.myqcloud.com/cnIXF1.jpg)

命令格式

keymaker XXXX-XXXX-XXXX-XXXXXXXXXXXX

中间有空格，生成一个XXXX-XXXX-XXXX-XXXXXXXXXXXX.key文件。

将这它重命名为：BTRS.key ，并拷贝到 U盘 config 目录下。

将引导U盘重新插入设备并开机，一切就结束了！！！
![VarF2U](https://blog-1251678165.cos.ap-chengdu.myqcloud.com/VarF2U.jpg)