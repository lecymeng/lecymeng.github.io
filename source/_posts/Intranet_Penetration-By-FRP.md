---
title: 使用FRP进行内网穿透
date: 2020-04-04 15:12:27
tags: [NAS, Network]
---

# 使用frp进行内网穿透
FRP项目地址：https://github.com/fatedier/frp
FRP中文说明：https://github.com/fatedier/frp/blob/master/README_zh.md

## frp是什么
简单地说:frp就是一个[反向代理软件](https://www.zhihu.com/question/24723688)，它体积轻量但功能很强大，可以**使处于内网或防火墙后的设备对外界提供服务**，它支持 **HTTP、TCP、UDP**等众多协议。

## 为什么需要内网穿透
**从公网中访问自己的私有设备向来是一件难事儿。**
自己的主力台式机、NAS等等设备，它们可能处于路由器后，或者运营商因为IP地址短缺不给你分配公网IP地址。如果我们想直接访问到这些设备（远程桌面，远程文件，SSH等等），一般来说要通过一些转发或者P2P组网软件的帮助。
我有一台计算机位于一个很复杂的局域网中，我想要实现远程桌面和文件访问，目前来看其所处的网络环境很难通过简单的端口映射将其暴露在公网之中，我试过这么几种方法：
<!--more-->

1. 远程桌面使用TeamViewer。可用，但需要访问端也拥有TeamViewer软件，不是很方便，希望能使用Windows自带的远程桌面。且TeamViewer不易实现远程文件访问。
2. 使用蒲公英VPN软件进行组网，可用，但免费版本网络速度极慢，体验不佳，几乎无法正常使用。
3. 使用花生壳软件进行DDNS解析，可用，但同第二点所述，免费版本有带宽限制，无法实际使用。
4. **搭建frp服务器进行内网穿透，可用且推荐，可以达到不错的速度，且理论上可以开放任何想要的端口，可以实现的功能远不止远程桌面或者文件共享。**

## 准备工作
搭建一个完整的frp服务链，我们需要

1. VPS一台（也可以是具有公网IP的实体机）
2. 访问目标设备（就是你最终要访问的设备，例如：PC，NAS，路由器）
3. 简单的Linux基础（会用cp等几个简单命令即可）

### VPS相关
- 因为frp的原理是利用服务端（所准备的VPS）进行转发，因而VPS的速度直接决定了之后连接的质量，请根据自己的需要选择相应主机配置。
- 本人使用了搬瓦工的洛杉矶机房，北京联通下测试速度也还过得去。
- 系统使用Ubuntu 16.04.5 x64，frp客户端和服务端本身同时均支持Linux和Windows，且配置方法一样，请根据实际环境自行测试，如果你是新建的VPS，那选择Ubuntu 16.04.5 x64就可以了。

## 服务端设置
SSH连接到VPS之后运行如下命令查看处理器架构，根据架构下载不同版本的frp

```bash
root@Weicools:/home# arch
x86_64
```

### 下载安装
这里选择的是Linux x86_64的版本：frp_0.32.1_linux_amd64.tar.gz
```bash
# 下载frp程序
root@Weicools:/home# 
wget https://github.com/fatedier/frp/releases/download/v0.32.1/frp_0.32.1_linux_amd64.tar.gz

# 解压
root@Weicools:/home# 
tar -zxvf frp_0.32.1_linux_amd64.tar.gz

# 改个名字
root@Weicools:/home# 
cp -r frp_0.32.1_linux_amd64 frp

# 进入frp程序目录
root@Weicools:/home# 
cd frp

root@Weicools:/home/frp# 
```

我们只需要关注如下几个文件
- frps 服务端程序
- frps.ini 服务端配置文件
- frpc 客户端程序
- frpc.ini 客户端配置文件

### 服务端配置
编辑frp服务端配置文件
```bash
root@Weicools:/home/frp# 
vim frps.ini
```

服务端配置文件内容根据需求自行配置，编辑完成后保存（vim保存如果不会请自行搜索）。

#### 简洁版本
```
[common]                                                                                                                   
bind_port = 7000
token = 12345678
```

#### 带Dashboard版本
```
[common]
bind_port = 7000
dashboard_port = 7500
token = 12345678
    
dashboard_user = admin
dashboard_pwd = admin
vhost_http_port = 10080
vhost_https_port = 10443
```

#### 配置注意事项
* `bind_port = 7000` 可以自行修改（如果没有必要，端口均可使用默认值），但要和接后面客户端配置文件中的 `bind_port` 相同，且服务端的7000端口需要放行，具体可以用宝塔面板（安全选项）进行设置，或者网上查询命令设置放行的方法。
* `dashboard_port` 是服务端仪表板的端口，若使用7500端口（需要放行），在配置完成服务启动后可以通过浏览器访问 x.x.x.x:7500 （其中x.x.x.x为VPS的IP）查看frp服务运行信息。
* `token` 是用于客户端和服务端连接的口令，请自行设置并记录，稍后会用到。
* `dashboard_user` 和 `dashboard_pwd` 表示打开仪表板页面登录的用户名和密码，自行设置即可。
* `vhost_http_port` 和 `vhost_https_port` 用于反向代理HTTP主机时使用，本文不涉及HTTP协议，因而照抄或者删除这两条均可。


### 运行frps服务端
配置完成服务端配置文件之后我们就可以运行frps的服务端了
```bash
root@Weicools:/home/frp# 
./frps -c frps.ini
```

如果看到屏幕输出一下这样的内容，即表示运行正常，如果出现错误提示，请检查上面的步骤。

```
2020/04/03 22:22:39 [I] [service.go:130] frps tcp listen on 0.0.0.0:7000
2020/04/03 22:22:39 [I] [service.go:172] http service listen on 0.0.0.0:10080
2020/04/03 22:22:39 [I] [service.go:193] https service listen on 0.0.0.0:10443
2020/04/03 22:22:39 [I] [service.go:216] Dashboard listen on 0.0.0.0:7500
2020/04/03 22:22:39 [I] [root.go:210] Start frps success
```

此时访问 `服务端IP:7500` 并使用自己设置的用户名密码登录，即可看到仪表板界面
![frp服务端仪表板界面](https://blog-1251678165.cos.ap-chengdu.myqcloud.com/FIRUx8.png)


### 服务端后台运行和自启
#### 后台运行方法-nohup
至此，我们的服务端仅运行在前台，如果Ctrl+C停止或者关闭SSH窗口后，frps均会停止运行，因而我们使用 [nohup命令](https://ehlxr.me/2017/01/18/Linux-的-nohup-命令的用法/)（nohup后台程序管理或关闭相关命令可自行查询资料）将其运行在后台。

```bash
root@Weicools:/home/frp# 
nohup ./frps -c frps.ini &

# 或者
root@Weicools:/home/frp# 
nohup ./frps -c frps.ini >/dev/null 2>&1 &
```

输出如下内容即表示正常运行
```
nohup: ignoring input and appending output to 'nohup.out'
```

此时可先使用 `Ctrl+C` 关闭 nohup，frps依然会在后台运行，使用 `jobs` 命令查看后台运行的程序，在结果中我们可以看到frps正在后台正常运行
```bash
root@Weicools:/home/frp# jobs
[1]+  Running    nohup ./frps -c frps.ini &
```

此时访问 x.x.x.x:7500 依然可以打开仪表板界面，至此，服务端即设置完成，就可以关闭SSH窗口了。

#### 后台运行方法-systemctl
##### 运行服务创建
```bash
root@Weicools:/home# 
vim /lib/systemd/system/frps.service
```

frps.service文件内容
```
[Unit]
Description=frps service
After=network.target syslog.target
Wants=network.target

[Service]
Type=simple
ExecStart=/home/frp/frps -c /home/frp/frps.ini

[Install]
WantedBy=multi-user.target
```

##### 启动后台服务
```bash
root@Weicools:/home# 
systemctl start frps
```

##### 开启自启动
```bash
root@Weicools:/home# 
systemctl enable frps
```

### 停止frp服务端
```bash
# 先找到这个进程
root@Weicools:/home# 
ps -aux|grep frp| grep -v grep
root    22416  0.0  1.2 114188 12432 ?    Sl    2019   1:05 ./frps -c ./frps.ini

# 然后kill -9 进程号
root@Weicools:/home# 
kill -9 22416
```

## 客户端设置
根据设备的情况选择相应的frp程序进行[下载](https://github.com/fatedier/frp/releases)

Windows下下载和解压等步骤不再描述。假定你下载了 `frp_0.32.1_windows_amd64.zip`，将其解压在了D盘根目录下，并且将文件夹重命名为 `frp`。



用文本编辑器打开frpc.ini，与服务端类似，内容如下。

```
[common]
server_addr = x.x.x.x
server_port = 7000
token = 12345678

[RDP]
type = tcp
local_ip = 127.0.0.1           
local_port = 3389
remote_port = 7001  

[SMB]
type = tcp
local_ip = 127.0.0.1
local_port = 445
remote_port = 7002
```

1. RDP即Remote Desktop 远程桌面，Windows的RDP默认端口是3389，协议为TCP，建议使用frp远程连接前，在局域网中测试好，能够成功连接后再使用frp穿透连接。
2. SMB即Windows文件共享所使用的协议，默认端口号445，协议TCP，本条规则可实现远程文件访问。

其中common字段下的三项即为服务端的设置。

- `server_addr` 为服务端IP地址，填入即可。
- `server_port` 为服务器端口，填入你设置的端口号即可，服务端配置的 `bind_port` 例如：7000
- `token` 是你在服务器上设置的连接口令，原样填入即可。


### 自定义规则
frp实际使用时，会按照端口号进行对应的转发，原理如下图所示。

![gZRQkk](https://blog-1251678165.cos.ap-chengdu.myqcloud.com/gZRQkk.jpg)

上面 `frpc.ini` 的 `Remote Desktop` `SMB` 字段都是自己定义的规则，自定义端口对应时格式如下
```
[xxx]
type = tcp
local_ip = 127.0.0.1           
local_port = 1234
remote_port = 5678 
```

- `[xxx]` 表示一个规则名称，自己定义，便于查询即可。
- `type` 表示转发的协议类型，有TCP和UDP等选项可以选择，如有需要请自行查询frp手册。
- `local_port` 是本地应用的端口号，按照实际应用工作在本机的端口号填写即可。
- `remote_port` 是该条规则在服务端开放的端口号，自己填写并记录即可。


### 运行frpc
配置完成frpc.ini后，就可以运行frpc了

```bash
# 使用命令提示符或Powershell进入该目录下
cd D:\frp`

# 执行
./frpc -c frpc.ini
```

运行frpc程序，窗口中输出如下内容表示运行正常。
```
2020/04/03 23:14:56 [I] [service.go:205] login to server success, get run id [2b65b4e58a5917ac], server udp port [0]
2020/04/03 23:14:56 [I] [proxy_manager.go:136] [2b65b4e58a5917ac] proxy added: [rdp smb]
2020/04/03 23:14:56 [I] [control.go:143] [smb] start proxy success
2020/04/03 23:14:56 [I] [control.go:143] [rdp] start proxy success
```

不要关闭命令行窗口，此时可以在局域网外使用相应程序访问 x.x.x.x:xxxx （IP为VPS的IP，端口为自定义的remote_port）即可访问到相应服务。

### 客户端后台运行及自启
#### Windows
frpc运行时始终有一个命令行窗口运行在前台，影响美观，我们可以使用一个批处理文件来将其运行在后台，而且可以双击执行，每次打开frpc不用再自己输命令了。
在任何一个目录下新建一个文本文件并将其重命名为`frpc.bat`，编辑，粘贴如下内容并保存。

```shell
@echo off
if "%1" == "h" goto begin
mshta vbscript:createobject("wscript.shell").run("""%~nx0"" h",0)(window.close)&&exit
:begin
REM
cd D:\frp
frpc -c frpc.ini
exit
```

将cd后的路径更改为你的frpc实际存放的目录。

之后直接运行这个 `.bat` 文件即可启动frpc并隐藏窗口（可在任务管理器中退出）。
至于开机启动，把这个 .bat 文件直接扔进Windows的开机启动文件夹就好了)
至此，Windows客户端配置完成，之后就是你自己根据需要在frpc.ini后追加规则即可。

客户端启动后台运行
```bash
nohup ./frpc -c frpc.ini &
nohup /mnt/user/moedata/frp/frpc -c /mnt/user/moedata/frp/frpc.ini &
```

客户端停止后台运行
```bash
ps -aux|grep frp| grep -v grep
```



