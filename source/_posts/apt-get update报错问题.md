---
title: apt-get update报错问题
date: 2014-11-12
tags: Linux
description: 本文记录了当年使用ubuntu桌面版时遇到的一个apt-get update报错问题。
---

```
W: 无法下载 http://ppa.launchpad.net/mc3man/trusty-media/ubuntu/dists/vivid/main/binary-amd64/Packages 404 Not Found
W: 无法下载 http://ppa.launchpad.net/mc3man/trusty-media/ubuntu/dists/vivid/main/binary-i386/Packages 404 Not Found
W: 无法下载 http://ppa.launchpad.net/noobslab/deepin-sc/ubuntu/dists/vivid/main/binary-amd64/Packages 404 Not Found
W: 无法下载 http://ppa.launchpad.net/noobslab/deepin-sc/ubuntu/dists/vivid/main/binary-i386/Packages 404 Not Found
E: 部分索引文件下载失败。如果忽略它们，那将转而使用旧的索引文件
```
此时可能是采用了较为古老的PPA仓库，删了就搞定了。方法如下

```
sudo add-apt-repository --remove ppa:mc3man/trusty-media
```

