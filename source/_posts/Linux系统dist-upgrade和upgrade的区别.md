---
title: linux系统dist-upgrade和upgrade的区别
date: 2015-12-29
tags: Linux
description: 本文简单介绍了Linux系统dist-upgrade和upgrade效果上的区别。
---

apt-get upgrade 和 apt-get dist-upgrade 本质上是没有什么不同的。

只不过，dist-upgrade 会识别出当依赖关系改变的情形并作出处理，而upgrade对此情形不处理。

例如软件包 a 原先依赖 b c d，但是在源里面可能已经升级了，现在是 a 依赖 b c e。这种情况下，dist-upgrade 会删除 d 安装 e，并把 a 软件包升级，而 upgrade 会认为依赖关系改变而拒绝升级 a 软件包。