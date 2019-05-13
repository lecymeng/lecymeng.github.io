---
title: RX470在macSierra10.12.2Beta3下的驱动方法
date: 2016-11-17 13:52:25
tags: [macSierra, 10.12.2, Hackintosh, Clover, RX470]
---

## 剁手开题
双十一凌晨在狗东买下的讯景RX470 4GB黑狼版1179块大洋，没赶上11.01凌晨的1099-100神价表示很蓝瘦…………香菇 =-=
<!--more-->
## 关键硬件信息
讯景RX470是2016双十一京东购入，BCM943602CS是2016.09.12淘宝购入，建兴T9 256GB是2016.03.24京东购入，显示器Sanc是2015.09.25天猫购入，鼠标是2015.04.09闲鱼购入，其他均为2015双十一期间在京东剁手买的

- CPU：英特尔（Intel）酷睿双核 i3-4370 -- 784大洋
- 主板：技嘉（GIGABYTE）B85M-D3H -- 399大洋
- 内存：十铨(Team) 冥神系列 DDR3 4GB*2 -- 297大洋(发票项目报账200，实际97)
- 硬盘：建兴T9 256GB(MLC) + 西数蓝盘1TB -- 444大洋 + 250大洋
- 显卡：讯景RX470 4GB + 集显HD4600 -- 1179大洋
- 电源：台达（DELTA）额定350W NX350 -- 159大洋
- 机箱：先马（SAMA）坦克(辣鸡，双十一又买了屌丝boU4) -- 149大洋(U4:319大洋)
- 散热器：超频三（PCCOOLER）东海X4 -- 69大洋
- 显示器：山寨XXX -- 599大洋
- 键盘：黑爵（Ajazz）极客AK33 -- 149大洋
- 鼠标：罗技G302 -- 150大洋(二手)
- 无线网卡：BCM943602CS -- 180大洋

## RX470/480驱动方法
### 1.修改驱动
![复制驱动](http://ogpsdets6.bkt.clouddn.com/2016-11-17-RX470Driver01.png)
![打开Info](http://ogpsdets6.bkt.clouddn.com/2016-11-17-RX470Driver02.png)
![修改Info](http://ogpsdets6.bkt.clouddn.com/2016-11-17-RX470Driver03.png)
![安装修改的驱动](http://ogpsdets6.bkt.clouddn.com/2016-11-17-RX470Driver04.png)
### 2.修改Clover配置文件config.plist
![Mount EFI](http://ogpsdets6.bkt.clouddn.com/2016-11-17-RX470Driver05.png)
![添加FakeID](http://ogpsdets6.bkt.clouddn.com/2016-11-17-RX470Driver06.png)
![Inject ATI](http://ogpsdets6.bkt.clouddn.com/2016-11-17-RX470Driver07.png)
### 3.设置BIOS
设置IGFX为启动显示设备（PCIe插槽1是独显RX470）

![SetGraphic](http://ogpsdets6.bkt.clouddn.com/2016-11-17-RX470Driver08.jpg)

### 4.启动macSierra
- 我的DVI-DVI线，插在独显上（虽然设置的是IGFX，但是不要插在集显上，否则进入系统虽然能看到独显已经驱动，但是使用的仍然是集显HD4600）

![Graphic](http://ogpsdets6.bkt.clouddn.com/2016-11-17-RX470Driver09.jpg)

- 由于我的是Windows10 + macSierra 10.12.2Beta3双系统，没有设置倒计时默认进入macSierra，所以开机会进入Clover需要选择手动系统，然而此时显示器无信号但是电脑是在工作的，所有我就按之前的启动界面（如下）

![CloverUI](http://ogpsdets6.bkt.clouddn.com/2016-11-17-RX470Driver10.jpg)

- 虽然看不到Clover我也可以按“向右”方向键“+回车键启动macSierra系统

- 稍等一会显示器就会有信号了，可以看到macSierra正在启动的进度条（固态硬盘开机还是很快的）

### 5.Geekbench4测试
- CPU测试：
![i3 4370](http://ogpsdets6.bkt.clouddn.com/2016-11-17-i3%204370.png)
- GPU测试：
![RX470gb4](http://ogpsdets6.bkt.clouddn.com/2016-11-17-RX470%20Geekbench.png)
![RX470rank](http://ogpsdets6.bkt.clouddn.com/2016-11-17-RX470Ranking.png)

### 6.系统展示
巴拉巴拉巴拉…………图片太多了

![](http://ogpsdets6.bkt.clouddn.com/2016-11-17-Show00.png)
![](http://ogpsdets6.bkt.clouddn.com/2016-11-17-Show01.png)
![](http://ogpsdets6.bkt.clouddn.com/2016-11-17-Show02.png)
![](http://ogpsdets6.bkt.clouddn.com/2016-11-17-Show03.png)
![](http://ogpsdets6.bkt.clouddn.com/2016-11-17-Show04.png)
![](http://ogpsdets6.bkt.clouddn.com/2016-11-17-Show10.png)
![](http://ogpsdets6.bkt.clouddn.com/2016-11-17-Show11.png)
![](http://ogpsdets6.bkt.clouddn.com/2016-11-17-Show12.png)
![](http://ogpsdets6.bkt.clouddn.com/2016-11-17-Show13.png)
![](http://ogpsdets6.bkt.clouddn.com/2016-11-17-Show14.png)
![](http://ogpsdets6.bkt.clouddn.com/2016-11-17-Show15.png)
![](http://ogpsdets6.bkt.clouddn.com/2016-11-17-Show16.png)
![](http://ogpsdets6.bkt.clouddn.com/2016-11-17-Show17.png)
![](http://ogpsdets6.bkt.clouddn.com/2016-11-17-Show18.png)
![](http://ogpsdets6.bkt.clouddn.com/2016-11-17-Show19.png)
![](http://ogpsdets6.bkt.clouddn.com/2016-11-17-Show20.png)

