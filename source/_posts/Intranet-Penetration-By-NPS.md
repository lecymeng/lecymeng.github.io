---
title: 使用NPS进行内网穿透
date: 2020-03-16 13:12:27
tags: [NAS, Network]
---

### 服务端
#### 下载NPS程序安装
```bash
# 运行如下命令，根据CPU架构，选择相应版本并进行下载
wget https://github.com/ehang-io/nps/releases/download/v0.26.6/linux_amd64_server.tar.gz

# 解压
tar -zxvf linux_amd64_server.tar.gz

# 文件夹改个名，方便使用
cp -r linux_amd64_server nps_server

# 进入nps_server
cd nps_server

# 开启Nps
./nps start
```

<!--more-->
#### Docker安装
```
# docker方式
docker run -d --name npsserver --net=host -v /home/npsconf:/conf ffdfgdfg/nps
```

#### nps.conf 关键配置
```
# Public password, which clients can use to connect to the server                                                          
# After the connection, the server will be able to open relevant ports and parse related domain names according to its own configuration file.                                                                                                        
public_vkey=abcdx

#web
web_host=1.2.3.4
web_username=UserName
web_password=xzy
web_port = 8012
```

### 客户端
```bash
root@WeicoolsNAS:/mnt/user/moedata/# 
mkdir NpsClient

root@WeicoolsNAS:/mnt/user/moedata/# 
cd NpsClient

root@WeicoolsNAS:/mnt/user/moedata/NpsClient# 
wget https://github.com/cnlh/nps/releases/download/v0.26.6/linux_amd64_client.tar.gz

root@WeicoolsNAS:/mnt/user/moedata/NpsClient# 
tar -zxvf linux_amd64_client.tar.gz

# 临时运行测试
root@WeicoolsNAS:/mnt/user/moedata/NpsClient# 
./npc -server=104.36.64.74:8024 -vkey=weicools@2233 -type=tcp

# 后台运行
root@WeicoolsNAS:/mnt/user/moedata/NpsClient# 
nohup ./npc -server=weicools.tk:8024 -vkey=weicools@2233 -type=tcp
nohup ./npc -server=weicools.tk:8024 -vkey=weicools@12138 -type=tcp

nohup ./npc -server=104.36.64.74:8024 -vkey=weicools@2233 -type=tcp
nohup ./npc -server=104.36.64.74:8024 -vkey=weicools@12138 -type=tcp
```

### NPS开机自启
#### 群晖
> https://www.jianshu.com/p/2f4d13636e38

```
Weicools@WeicoolsSyno:~$ sudo -i
vi /usr/syno/etc.defaults/rc.sysv/autonps.sh
	input: /var/services/homes/Weicools/NpsClient/./npc -server=weicools.tk:8024 -vkey=weicools@12138 -type=tcp

vi /etc/rc
	input(:$): 
	# Auto start nps client
	/usr/syno/etc.defaults/rc.sysv/autonps.sh
```

#### Unraid
https://www.vediotalk.com/archives/4184