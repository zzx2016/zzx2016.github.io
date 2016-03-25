---
title: Git常用命令
date: 2016-03-25 14:14:18
tags: Git
---

一下罗列的只是平时常用的命令，更高级详尽的命令请参考[廖雪峰](http://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000/)的教程，没有最牛逼只有更牛逼。

设置git的user name和email
```
git config --global user.name "xxx"
git config --global user.email "xxx@gmail.com"
```

查看git的参数设置
```
git config --list
```

生存密钥：
```
ssh-keygen -t rsa -C "xxx@gmail.com"
```

创建空的 Git 目录
```
git init
```

clone远程仓库
```
git clone https://github.com/XXX/YYY.git
```

追踪test.txt
```
git add test.txt
或者
git add -A
```

将修改内容提交到本地仓库
```
git commit -m "描述信息"
```

将本地仓库push到远程分支XXX
```
git push origin XXX
```

查看当前状态
```
git status
```

新建分支
```
git branch XXX
或者
git checkout -b XXX
```

切换分支
```
git checkout XXX
```

将XXX分支merge到当前分支
```
git merge XXX
```
