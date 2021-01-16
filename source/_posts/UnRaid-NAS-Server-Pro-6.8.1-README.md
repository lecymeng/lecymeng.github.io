---
title: unRAID NAS Server Pro 6.8.1 快乐版
date: 2020-04-06 11:55:00
tags: [NAS, Unraid]
---

# unRAID NAS Server Pro 6.8.1 快乐版

<!--[unRAID NAS Server Pro 6.8.2 开心版](http://www.hopol.cn/2020/01/1510/)-->

下载地址：[MEGA](https://mega.nz/folder/kv4w0YJA#YtT-Kr3lfa2zDPrEOpSEyw)

## 解压后截图

![Crms8X](https://blog-1251678165.cos.ap-chengdu.myqcloud.com/Crms8X.jpg)

## 食用方式

- 将 U 盘格式化为 FAT32 格式，名称为：UNRAID
- 将解压出来的所有文件拷贝到 U 盘根目录下
  ![VQXhuT](https://blog-1251678165.cos.ap-chengdu.myqcloud.com/VQXhuT.jpg)
- 用管理员权限运行 make_bootable.bat
- 将 U 盘插入设备并启动，正常引导之后会看到当前获取到的 IP。
<!--more-->
- 我这里直接插入网线，上级有 DHCP 服务器，所以直接获取到局域网 IP。
- 浏览器输入 IP，默认打开的页面就能看到 Flash GUID，格式为 `XXXX-XXXX-XXXX-XXXXXXXXXXXX`，复制备用。
  ![QhfdHI](https://blog-1251678165.cos.ap-chengdu.myqcloud.com/QhfdHI.jpg)

- 拔下引导 U 盘，插入 PC 端，输入如下命令格式：`keymaker XXXX-XXXX-XXXX-XXXXXXXXXXXX`
  ![cnIXF1](https://blog-1251678165.cos.ap-chengdu.myqcloud.com/cnIXF1.jpg)

- 中间有空格，生成一个 XXXX-XXXX-XXXX-XXXXXXXXXXXX.key 文件。
- 将这它重命名为：BTRS.key ，并拷贝到 U 盘 config 目录下
- 将引导 U 盘重新插入设备并开机，一切就结束了！！！
  ![VarF2U](https://blog-1251678165.cos.ap-chengdu.myqcloud.com/VarF2U.jpg)
