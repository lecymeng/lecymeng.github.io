---
title: IADB 扩展
date: 2020-01-06 21:20:00
password: bcac371b54f59945a14aa49e2e408e5d6e4dbc59387f5d8cfc6b015d40d5bb02
tags: [Android, Script]
---

# IADB 工具
ADB扩展工具

含义：i adb && IA Debug Bridge
实现步骤：
1. Shell脚本：iadb.sh（调用adb命令）
2. 二进制程序：iadb（使用shc封装成可执行程序）
<!--more-->

![](https://blog-1251678165.cos.ap-chengdu.myqcloud.com/Ns2t4O.png)

## 参数1

| Param     | AppName     | PackageName                       |
|-----------|-------------|-----------------------------------|
| sp        | SpaceK      | com.oneapp.max.cleaner.booster.cn |
| op        | Optimizer-K | com.oneapp.max.cn                 |
| pp        | PPP-K       | com.oneapp.max.security.pro.cn    |
| wa        | Walk-K      | Com.walk.sports.cn                |

## 参数2

| Param     | 说明                             | 使用 |
|-----------|--------------------------------|----|
| rp        | Revoke Permissions             |    |
| rp-r      | Revoke Permissions and Restart |    |
| uninstall | Uninstall App                  |    |
| kill      | Kill App                       |    |
| start     | Start App                      |    |
| restart   | Restart App                    |    |
| clear     | Clear App Data                 |    |
| clear-r   | Clear App Data and Restart     |    |

### Revoke Permissions
### Revoke Permissions and Restart
### Uninstall App
```
adb uninstall [-k] <packagename>
```

<packagename> 表示应用的包名，-k 参数可选，表示卸载应用但保留数据和缓存目录

### Kill App
### Start App
### Restart App

### Clear App Data
```
adb shell pm clear <packagename>
```

`<packagename>` 表示应用名包，这条命令的效果相当于在设置里的应用信息界面点击了「清除缓存」和「清除数据」

### Clear App Data and Restart

脚本的好处是便捷、高效，拿起来就可以写，写完就能跑，都不用编译

但坏处也显而易见，一些敏感的、不想让外人知道的东西都是明文写在里面的，所以，在这推荐一款神奇的脚本封装程序——shc：

```bash
brew install shc
```

日常用法：

```bash
shc -r -f /shellfile.sh
```

运行成功后会在当前目录下生成两个文件：

```
shellfile.sh.x
shellfile.sh.c
```

`shellfile.sh.x`是脚本所对应的可执行程序

`shellfile.sh.c`是`shellfile.sh.x`对应的c语言实现的源码

shc根据脚本文件的第一行`#!/bin/bash`或其他shell将脚本翻译成相应的c源码并生成可执行程序。

但shc似乎无法识别expect

封装后的脚本安全性会有所提高，但这也仅能防个君子，通过gdb或其他调试工具仍然能获得最初的源码


test adb
