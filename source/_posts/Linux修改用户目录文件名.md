---
title: Linux修改用户目录文件名
date: 2016-04-26
tags: Linux
description: 很多时候，我们为了方便安装了中文版的发行版，但是此时用户目录也变成了中文，`cd 下载` 用起来真的很难受，所以我们希望可以将目录名修改为英文或者任意自定义。
---


很多时候，我们为了方便安装了中文版的发行版，但是此时用户目录也变成了中文，`cd 下载` 用起来真的很难受，所以我们希望可以将目录名修改为英文或者任意自定义。
##方法一
适用于绝大多数主流linux（不推荐）

```
    export LANG=en_US  
    xdg-user-dirs-gtk-update  
    export LANG=zh_CN  
```

##方法二
适用于全部linux，并支持目录名自定义（推荐）

xdg支持以下关键字对应缺省的目录：

```
DESKTOP
DOWNLOAD
TEMPLATES
PUBLICSHARE
DOCUMENTS
MUSIC
PICTURES
VIDEOS
```

所以，修改桌面的命令就是

```
xdg-user-dirs-update --set DESKTOP /home/你的用户名/desktop
```

其余类似，注意最后一个参数要是完整目录路径

设置完成后，因系统而异，有的系统需要重启才能生效

相关配置项存在于`/etc/xdg/user-dirs.conf` 和 `~/.config/user-dirs.dirs`
