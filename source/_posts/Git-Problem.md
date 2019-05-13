---
title: Git使用填坑
date: 2018-01-12 14:16:27
tags: [Git, Github, 填坑]
---

## Git使用常见错误
> 填坑，Git使用过程碰到的常见错误

### Git配置
<!--more-->

```bash
WeicoolsdeiMac:~ weicools$ git config --global user.name "xxxx"
WeicoolsdeiMac:~ weicools$ git config --global user.email "yyyy@abc.com"

WeicoolsdeiMac:~ weicools$ mkdir .ssh
WeicoolsdeiMac:~ weicools$ cd ~/.ssh

WeicoolsdeiMac:.ssh weicools$ ssh-keygen -t rsa -C "xxxx@qq.com"
Generating public/private rsa key pair.
Enter file in which to save the key (/Users/weicools/.ssh/id_rsa): qwe
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in qwe.
Your public key has been saved in qwe.pub.
The key fingerprint is:
SHA256:6RCdRQI6ob4Ouuj2BfGQKi7X1x6I9zjIqBEStCRzYjs xxxx@qq.com
The key's randomart image is:
+---[RSA 2048]----+
|++. . ....o      |
|=+o..o . +       |
|.E.+o . o        |
| oo +. . .       |
|+... .. S        |
|+. o.. =         |
|+.oooo+ +        |
|+*. +o.+ .       |
|B+o.  ..o        |
+----[SHA256]-----+

WeicoolsdeiMac:.ssh weicools$ cat ~/.ssh/qwe.pub
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDMIKGkVEP/TtXelV1PVL4489as7U82i70cLwWqWT4OZMabpRO5jJwjbStmoUaWfMYOaKFk4EUxZA9010/FNRhMnNPw3nvt++DQf1Cyidh8SJZl/6paFAW7I+hnIcwo8xbkvPJ66Wf96sHMXbhtHelFp4iUDIQUDdWVLSJfSjEVv8FSV7AX0f6f9Mrt2AqBUCsEPRWYAjRMeusdCPv176OTy9B4j7BGvfXkSiF8NnLK71TzmLs6MAcRTuHpJ79NupM8L6mMSg0gI0H+3/S4fr0xHu25oQwKYDm6PIJjdLQ05upToYz/EqRzDpG6q4L/e2mp5nCYrm5pbBzjTpQni31D xxxx@qq.com
```

###  使用Git创建一个项目和提交过程

```
$ makdir ~/hello-world    //创建一个项目hello-world
$ cd ~/hello-world       //打开这个项目
$ git init             //初始化 
$ touch README
$ git add README        //更新README文件
$ git commit -m 'first commit'     //提交更新，并注释信息“first commit” 
$ git remote add origin git@github.com:xxx/yyyy.git     //连接远程github项目  
$ git push -u origin master     //将本地项目更新到github项目上去
```

### 检查你的设置(Checking Your Settings)
`WeicoolsdeiMac:.ssh weicools$ git config --list`
`WeicoolsdeiMac:.ssh weicools$ git config user.name`

## Problem
1. counld not read remote repository
   `$ git remote set-url origin https://github.com/YourUserName/YatouTest00123.git`
   `git branch --set-upstream-to origin/master master`

2. 找不到仓库(Private): `git clone https://username:password@github.com/lecymeng/BizportDemo.git`
3. 添加仓库: `git remote add origin https://github.com/lecymeng/BizportDemo.git`
4. 如果输入   `$ git remote add origin git@github.com:djqiang（github帐号名）/gitdemo（项目名）.git`   提示出错信息：fatal: remote origin already exists.
    * 解决办法如下：
    * 1、先输入$ git remote rm origin
    * 2、再输入$ git remote add origin git@github.com:djqiang/gitdemo.git 就不会报错了！
    * 3、如果输入$ git remote rm origin 还是报错的话，error: Could not remove config section 'remote.origin'. 我们需要修改gitconfig文件的内容
    * 4、找到你的github的安装路径，我的是C\Users\ASUS\AppData\Local\GitHub\PortableGit_ca477551eeb4aea0e4ae9fcd3358bd96720bb5c8\etc
    * 5、找到一个名为gitconfig的文件，打开它把里面的[remote "origin"]那一行删掉就好了！
5. 如果输入   `$ ssh -T git@github.com`   出现以下提示：可以把`192.30.255.113 github.com`添加到Hosts

    ```
    weicools@Weicoolss-MacBook-Pro  ~/.ssh  ssh -T git@github.com
    The authenticity of host 'github.com (192.30.255.113)' can't be established.
    RSA key fingerprint is SHA256:nThbg6kXUpJWGl7E1IGOCspRomTxdCARLviKw6E5SY8.
    Are you sure you want to continue connecting (yes/no)? yes
    Warning: Permanently added 'github.com,192.30.255.113' (RSA) to the list of known hosts.
    Permission denied (publickey).
    ```
5. 如果输入   `$ ssh -T git@github.com`   出现错误提示：Permission denied (publickey).因为新生成的key不能加入ssh就会导致连接不上github。
    * 解决办法如下：
    * 1、先输入`$ ssh-agent`，再输入`$ ssh-add ~/.ssh/id_key`，这样就可以了。
    
        ```
        weicools@Weicoolss-MacBook-Pro  ~  sudo ssh-add ~/.ssh/moe
        Enter passphrase for /Users/weicools/.ssh/moe:
        Identity added: /Users/weicools/.ssh/moe (/Users/weicools/.ssh/moe)
        weicools@Weicoolss-MacBook-Pro  ~  ssh -T git@github.com
        Hi lecymeng! You've successfully authenticated, but GitHub does not provide shell access.
        weicools@Weicoolss-MacBook-Pro  ~ 
        ```
    * 2、如果还是不行的话，输入ssh-add ~/.ssh/id_key 命令后出现报错Could not open a connection to your authentication agent.解决方法是key用Git Gui的ssh工具生成，这样生成的时候key就直接保存在ssh中了，不需要再ssh-add命令加入了，其它的user，token等配置都用命令行来做。
    * 3、最好检查一下在你复制id_rsa.pub文件的内容时有没有产生多余的空格或空行，有些编辑器会帮你添加这些的。
6. 如果输入   `$ git push origin master`   提示出错信息：error:failed to push som refs to .......
    * 解决办法如下：
    * 1、先输入$ git pull origin master //先把远程服务器github上面的文件拉下来
    * 2、再输入$ git push origin master
    * 3、如果出现报错 fatal: Couldn't find remote ref master或者fatal: 'origin' does not appear to be a git repository以及fatal: Could not read from remote repository.
    * 4、则需要重新输入$ git remote add origin git@github.com:xxx/yyy.git



