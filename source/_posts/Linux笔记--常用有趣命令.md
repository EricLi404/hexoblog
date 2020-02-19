---
title: Linux 笔记--常用有趣命令
date: 2014-11-15
tags: Linux
description: sl 、 cmatrix 、aafire 。。。。。
---

## 小火车 ##
安装命令

```
sudo apt-get install sl
yum -y install sl
```
使用命令

```
sl
```
##漫天火苗##
安装命令

```
sudo apt-get install libaa-bin
``` 
使用命令

```
aafire
```
##代码刷屏##
安装命令

```
sudo apt-get install cmatrix
```
使用命令

```
cmatrix
```
##ascii水族馆##
安装命令

```
sudo apt-get install libcurses-perl
cd /tmp
wget http://search.cpan.org/CPAN/authors/id/K/KB/KBAUCOM/Term-Animation-2.4.tar.gz
tar -zxvf Term-Animation-2.4.tar.gz
cd Term-Animation-2.4/
perl Makefile.PL && make && make test
make install
cd /tmp
wget http://www.robobunny.com/projects/asciiquarium/asciiquarium.tar.gz
tar -zxvf asciiquarium.tar.gz
cd asciiquarium_1.1/
cp asciiquarium /usr/local/bin
chmod 0755 /usr/local/bin/asciiquarium
```
使用命令

```
asciiquarium
```


##老牛说话##
 *一个为桌面环境使用，一个为tty使用*

安装命令
```
sudo apt-get install cowsay
sudo apt-get install xcowsay
```
使用命令

```
cowsay hello
xcowsay hello
```

*cowsay或者xcowsay后边加上要让老牛说的话*

