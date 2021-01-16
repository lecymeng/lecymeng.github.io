---
title: 端口号占用解决方法
date: 2020-03-06 22:25:00
tags: [Linux, Network]
---

# 端口号占用解决方法

## Linux 端口号占用解决方法

### 查询端口号

1. 使用`netstat`命令
   netstat -tunlp 会显示所有端口和所有对应的程序，如果像得到自己关系的结果，可以使用 grep 对结果集进行过滤。例如：

```bash
netstat -tunlp | grep java
```

这样就能过滤出只带有 java 进程的所有端口被占用的情况。

<!--more-->

2. 使用`lsof -i:某个端口`

```bash
# 查看22号端口被占用的程序
lsof -i:22

# 通过上述命令，可以查看到相应的进程号，如果还想查看该进程的详细信息可以使用：
ps -ef | grep 进程号
```

### 杀掉进程

```bash
kill -9 PID号
```

## windows 端口占用解决方法

查看所有的端口占用情况

```bash
netstat -ano
```

查找指定端口的占用情况
例如查找 8080 端口：

```bash
netstat -ano | findstr "8080"
```

通过查找端口号就能找到相应的进程号，通过查找相应的进程号就能找到相应进程的详细信息
查看对应 PID 对应的进程

```bash
tasklist | findstr "5555"
```

通过 tasklist 可以列出具体的进程信息
通过进程名，停止进程

```bash
taskkill /f /t /im xxx.exe
taskkill -PID <进程号> -F //强制关闭某个进程 ，例如：taskkill -PID 5555 -F
```
