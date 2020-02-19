---
title: ubuntu-unity桌面禁用客人会话
date: 2015-09-14
tags: Linux
description: 本文记录了在ubuntu-unity桌面上禁用客人会话的方式。
---


ubuntu-unity桌面禁用客人会话
====================

1. 新建配置文件
----------------------

```
sudo vi /etc/lightdm/lightdm.conf
```
其内容为
```
[SeatDefaults]
greeter-session=unity-greeter
allow-guest=false
```

2. 编译
-----

```
sudo sh -c 'printf "[SeatDefaults]\nallow-guest=false\n" >/usr/share/lightdm/lightdm.conf.d/50-no-guest.conf'
```


针对此方法恢复客人会话的操作为
---------------

```
sudo rm -f /usr/share/lightdm/lightdm.conf.d/50-no-guest.conf
```

