---
title: 暗影精灵2Plus GTX1060独显亮度调节实现
date: 2017-08-26 01:47:53
tags: [macOS, Hackintosh]
---

# 暗影精灵 2Plus GTX1060 独显亮度调节实现

> Foreword：本来打算毕业工作了再换本的，但是实在受不了之前那个低压 U 本 Yoga13 了，开个 IDE 和模拟器就快炸了，玩个 LOL 一打团就变 PPT 玩个蛇皮；然后然后看到**`暗影精灵2Plus`**七代 U 版本比首发价还低 1000 就忍不住剁手了 🤣，总体体验：性能不用说，i7+GTX1060 甜品显卡基本都可以玩，72％色域 IPS 屏幕也是很棒，缺点 17.3 寸比较重、台达电源 230W 好大一块砖、键盘方向键反人类 🙃
> ![Order](https://blog-bak-1251678165.cos.ap-beijing.myqcloud.com/2017-08-27-023638.jpg)
> 回到主题：这本惠普自己把**`核显HD630屏蔽`**了，Windows 设备管理器都看不到（Windows 上不插电源没法玩游戏），Hackintosh 岂不是可以直接用 GTX1060 了 😉 美滋滋，虽然是用上了但是**`亮度调节`**一直不行，直到昨天才搞定。

<!--more-->

1. 本次参考的教程：
   - [【笔记本 GTX1060】现在弄好了调节亮度啦-远景论坛-微软极客社区](http://bbs.pcbeta.com/viewthread-1742873-9-1.html)
   - [蓝天 p6xxrx 准系统 EFI 10.12 （神舟战神 z7s3）-远景论坛-微软极客社区](http://bbs.pcbeta.com/viewthread-1714158-1-1.html)
   - [GTX970M-原生亮度调节-远景论坛-微软极客社区](http://bbs.pcbeta.com/viewthread-1663916-1-1.html)
   - [轻松注入 EDID 参数-远景论坛-微软极客社区](http://bbs.pcbeta.com/viewthread-1473634-1-1.html)
2. 使用到的工具：
   - [DarwinDumper_v3.0.3](https://bitbucket.org/blackosx/darwindumper/downloads/)
   - [注入 EDID 的利器：FixEDID 2.3.2(12 楼已有热心会员做的汉化版)](http://bbs.pcbeta.com/forum.php?mod=viewthread&tid=1525657)
   - [MaciASL DSDT 和 SSDT 工具](https://bitbucket.org/RehabMan/os-x-maciasl-patchmatic/downloads/)
   - [Acpica iasl 反编译脚步工具](https://bitbucket.org/RehabMan/acpica/downloads/)
   - [EasyKext Pro 驱动安装和权限修复](http://www.insanelymac.com/forum/files/file/397-easykext-pro-a-minimal-and-super-fast-kext-installer/)

## 一.注入 EDID

1. 打开 DarwinDumper_v3.0.3 直接点击 Run（也可只选 EDID）
   ![DarwinDumper_v3.0.3](https://blog-bak-1251678165.cos.ap-beijing.myqcloud.com/2017-08-27-031521.jpg)
   ![Report](https://blog-bak-1251678165.cos.ap-beijing.myqcloud.com/2017-08-27-031613.jpg)

   > 之前我用教程带的 DarwinDumper2.8.8 生成的 bin 文件在 FixEDID2.3 导入报错，2.3 版本目测选 hex 文件会有问题，坑啊 🙃，还好去找到了最新的版本 3.0.3 用起来没啥问题

2. 打开 FixEDID 2.3.2，打开 EDID 文件选择刚才生成的 EDID.bin，其他需要勾选的图上已做标注：选择显示器型号，下面两个都选 AppleBlacklightDisplay，然后点击 make 得到三个东西：DisplayVendorID-xxx 文件夹、DisplayMergeNub.kext、EDID-xxx.bin。然后需要做的是`把驱动安装到S/L/E并修复缓存` 或者 `把DisplayVendorID-xxx文件夹放到/System/Library/Displays/Contents/Resources/Overrides目录下` 推荐使用驱动注入，重新启动看看显示器情况。
   ![FixEDID](https://blog-bak-1251678165.cos.ap-beijing.myqcloud.com/2017-08-26-155155.jpg)
   > 坑点：把 FixEDID 中显示器型号都试了一遍，生成的驱动安装到 S/L/E 均无效，功能扩展显示`签名验证错误`
   > ![Kextsign](https://blog-bak-1251678165.cos.ap-beijing.myqcloud.com/2017-08-26-134239.jpg)
   > 无奈只能选择把 **显示器的 Overrides 文件** 扔到 `/System/Library/Displays/Contents/Resources/Overrides` ，这种方式也存在不少问题，多数型号开机卡住（强制关机然后-V 模式可进入），部分是没有任何效果，只有 MacBook Pro 16:10 可行，当时效果如下：
   > ![MBP](https://blog-bak-1251678165.cos.ap-beijing.myqcloud.com/2017-08-26-154719.jpg)
   > 17.3 寸显示器显示成了 13.3 寸 🙃

## 二.DSDT 部分

1. 打补丁：Brightness fix（加入 PLNF 代码，记得先把已存在的 PLNF 重命名）
   ![Brightness](https://blog-bak-1251678165.cos.ap-beijing.myqcloud.com/2017-08-26-103057.jpg)
2. 最关键地方：打开 IORegistryExplorer 搜索 display，查看 AppleDisplay 所在位置
   ![AppleDisplay](https://blog-bak-1251678165.cos.ap-beijing.myqcloud.com/2017-08-23-040403.jpg)
   打开 MaciASL，选择相应 DSDT 文件，在 DSDT 下的 PEG0 下 PEGP 总加入\_DSM 方法，注入代码如下:

   ```
   Method (_DSM, 4, NotSerialized)  // _DSM: Device-Specific Method
   {
      If (LEqual (Arg2, Zero))
      {
          Return (Buffer (One)
          {
               0x03
          })
      }

      Return (Package (0x16)
      {
          "@1,AAPL,boot-display",
          Buffer (One)
          {
               0x01
          },

          "@1,backlight-control",
          Buffer (0x04)
          {
               0x01, 0x00, 0x00, 0x00
          },

          "@1,built-in",
          Buffer (One)
          {
               0x01
          },

          "@0,connector-type",
          Buffer (0x04)
          {
               0x00, 0x08, 0x00, 0x00
          },

          "@1,connector-type",
          Buffer (0x04)
          {
               0x00, 0x04, 0x00, 0x00
          },

          "@2,connector-type",
          Buffer (0x04)
          {
               0x00, 0x04, 0x00, 0x00
          },

          "@3,connector-type",
          Buffer (0x04)
          {
               0x00, 0x04, 0x00, 0x00
          },

          "@4,connector-type",
          Buffer (0x04)
          {
               0x00, 0x04, 0x00, 0x00
          },

          "@5,connector-type",
          Buffer (0x04)
          {
               0x00, 0x04, 0x00, 0x00
          },

          "@1,pwm-info",
          Buffer (0x14)
          {
              /* 0000 */  0x01, 0x14, 0x00, 0x64, 0xA8, 0x61, 0x00, 0x00,
              /* 0008 */  0x1E, 0x02, 0x00, 0x00, 0x2C, 0x00, 0x00, 0x00,
              /* 0010 */  0x00, 0x04, 0x00, 0x00
          },

          "hda-gfx",
          Buffer (0x0A)
          {
              "onboard-1"
          }
      })
   }
   ```

   > 注意："@1,AAPL,boot-display", "@1,backlight-control","@1,built-in","@1,pwm-info"这几个地方，@1 是显示器所在位置，根据 IORegistryExplorer 显示：我的是在 NVDA,Display-B@1。
   > ![DSDT AppleDisplay](https://blog-bak-1251678165.cos.ap-beijing.myqcloud.com/2017-08-27-DSDT%20AppleDisplay.png)
   > 插入完代码保存 DSDT，把它放到`/EFI/CLOVER/ACPI/patched`，Clover 中勾选注入即可，重启即可看到加载的是 AppleBlacklightDisplay
   > ![AppleBlacklightDisplay](https://blog-bak-1251678165.cos.ap-beijing.myqcloud.com/2017-08-27-043716.jpg)

## 三.替换驱动

1. 10.12.4 下可不用替换 AppleBacklight.kext，但是 10.12.5/10.12.6 需要替换，10.13 未知，驱动分享链接: https://pan.baidu.com/s/1mhPjxqS 密码: 6666
2. 使用 EasyKext 安装修复缓存即可。

## 四.效果展示

完成上面三步，基本上就是 OK 了~重启电脑试试吧 😉😉😉！！！（我的亮度直接是可保存的，若不可以请参考：[[分享] 解决开机第二阶段花屏和 Clover 脚本不生成 nvram.plist 导致不能保存亮度的问题](http://bbs.pcbeta.com/viewthread-1538926-1-1.html)）

![概览](https://blog-bak-1251678165.cos.ap-beijing.myqcloud.com/2017-08-27-043359.jpg)
![显示器图形卡](https://blog-bak-1251678165.cos.ap-beijing.myqcloud.com/2017-08-27-043512.jpg)
![亮度调节1](https://blog-bak-1251678165.cos.ap-beijing.myqcloud.com/2017-08-27-043606.jpg)
![亮度调节2](https://blog-bak-1251678165.cos.ap-beijing.myqcloud.com/2017-08-27-044609.jpg)
![亮度调节3](https://blog-bak-1251678165.cos.ap-beijing.myqcloud.com/2017-08-27-044641.jpg)

> **差不多就结束了吧，踩得坑还是不少，尤其是注入 EDID，之前一直不行所有放弃放弃……，但是看到论坛有不少人都可以实现独显亮度调节，忍不住又回来重试了好几遍，折腾啊……**🙃🙃🙃
