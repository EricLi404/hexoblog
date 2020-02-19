---
title: MySQL所有者不是mysql的解决方案
date: 2018-01-22
tags: [macOS,database,MySQL,Linux]
description: 之前解决了 brew 的权限问题，今天发现在解决的过程中一不小心把 MySQL 的权限搞丢了。。。。
---

之前解决了 brew 的权限问题，今天发现在解决的过程中一不小心把 MySQL 的权限搞丢了。
前情提要：[mac brew 权限问题解决记录](http://blog.ericli.top/2018/01/18/mac%20brew%20权限问题解决记录/)

在给 brew 权限的过程中，把 `/usr/local/mysql` 的的所有权一不小心也给了 brew  。

所以导致报错：

```
Warning:The /usr/local/mysql/data directory is not owned by the 'mysql' or '_mysql' user.
```

如图：
![](https://ws1.sinaimg.cn/large/006tNc79ly1fnpc5vydl1j30ik08qdgw.jpg)

**解决方案**
改所有者喽。。。

```
sudo chown -R mysql:mysql /usr/local/mysql
sudo chown -R mysql:mysql /usr/local/mysql/data
```
EOF