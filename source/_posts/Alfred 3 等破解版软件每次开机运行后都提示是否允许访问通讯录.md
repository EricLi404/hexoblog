---
title: Alfred 3 等破解版软件每次开机运行后都提示是否允许访问通讯录
date: 2017-11-09
tags: macOS
description: 和谐版的Alfred 3 在每次开机后，都会提示“是否允许访问通讯录”的弹窗，让人不胜其烦。本文记录了其解决方案，方案来自[xclient.info](http://xclient.info)。
---


和谐版的Alfred 3 在每次开机后，都会提示“是否允许访问通讯录”的弹窗，让人不胜其烦。
这是因为和谐片的App丢失了签名导致不会自动加入系统。



以 Alfred 3 为例的处理方法：
```
sudo codesign -f -d -s - /Applications/Alfred\ 3.app/Contents/Frameworks/Alfred\ Framework.framework/Versions/A/Alfred\ Framework
```

感谢[xclient.info](http://xclient.info)