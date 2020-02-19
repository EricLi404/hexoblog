---
title: Oracle DBMS占用大量内存的解决方法
date: 2016-09-15
tags: database
description: 本文记录了笔者曾经遇到的Oracle DBMS占用大量内存的解决方法。
---

本学期学习oracle数据库，还学习android开发，于是电脑上装了oracle 12，还装了android studio以及多个genymotion模拟器，然后开启了ssd的rapid模式（使用部分闲置内存作为ssd的快速缓冲区，硬盘速度简直炸裂，seq顺序读写的速度大概是hdd的30倍，4k随机速度达到了hdd的500倍。。。上图装逼，不服单劈），![这里写图片描述](http://img.blog.csdn.net/20160911084330973)
好，故事背景就写到这，下面进入主题，在我打开android studio或者phpstorm的时候，常出现内存90%占用，而且，，有时候开机内存直接占用60%，于是我打开了任务管理器，看看谁是内存大户，，结果发现。。。rapid，android studio居然都不是，，，oracle dbms居然占了我4个G的内存。。于是，杀意顿起。。。。。

好吧，。，，

这才是真正的主题，。，。

如果你发现你的oracle dbms占用大量内存。。。建议你给他做个限制

首先dba身份登录，用sqlplus就可以，

然后输入

```
show parameter sga;     //显示最大分配内存
```

在`sga_max_size` 下，你可以看到你当前的内存分配值，

如果觉得太大，

可以用

```
alter system set sga_max_size=$size scope=spfile;

```

 
修改内存占用大小，修改完了需要重启才能生效。
 
ps：附上用dba身份登录数据库的方法（win下）




方法1。

 
```
1.打开cmd
2.输入   sqlplus  / nolog 回车
3.输入   connect  / as sysdba  回车
```

方法2.

```
sqlplus sys/password as sysdba
 
```

 

 

 

 

 
 

 

 

 
 

 