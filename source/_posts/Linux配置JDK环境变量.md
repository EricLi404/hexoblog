---
title: Linux配置JDK环境
date: 2016-04-26
tags: [Java,Linux]
description: 本文记录了笔者初学Linux时配置JDK环境的过程。
---

##1.
首先下载jdk，解压，放到想放的目录，我放到了`/usr/local/jdk1.8.0_91`

> tar.gz 的解压指令为`tar zxvf + 文件名`
##2.
然后修改配置文件`/etc/profile`，在最后添加

```
// JAVA_HOME 真实路径请根据个人版本设置
JAVA_HOME=/usr/local/jdk1.8.0_91
PATH=$JAVA_HOME/bin:$PATH
export JAVA_HOME PATH

```

##3.
使刚才的配置生效
注意：此时需要在root模式和普通用户模式都执行一下该命令，保证root用户和普通用户的环境变量都配置正常
```
source /etc/profile
```

##4.
检验是否成功

输入`java -version`

提示：

```
java version "1.8.0_91"
Java(TM) SE Runtime Environment (build 1.8.0_91-b14)
Java HotSpot(TM) 64-Bit Server VM (build 25.91-b14, mixed mode)

```

则证明安装成功