---
title: macOS 缺少 Freetype 终极解决方案
date: 2016-11-03
tags: [PHP,macOS,Apache]
description: 使用 macOS 自带的 Apache 和 PHP 运行 ThinkPHP 时，发现验证码模块无法正常使用。本文总结了多个该问题解决方案。
---

`macOS`自带`php`的`gd库`有些问题，当用到`imagettftext()` 和 `freetype` 相关功能时会出问题，我遇到的问题是：

 在使用`TinkPHP`的 `topthink/think-captcha` 时，验证码图片无法正常显示，`ThinkPHP`中`log` 信息为：
 

```
[ 2017-12-28T16:38:06+08:00 ] 127.0.0.1 GET /captcha.html
[ error ] [0]致命错误: Call to undefined function think\captcha\imagettftext()
```

大致意思就是没有 `imagettftext()`这个函数，这是因为缺少 `php` 的 `gd库` 中的 `freetype` 模块。

网上的解决方案大致有这么几种：

-  重新编译 php
-  用 brew 安装一个新的 php
-  https://php-osx.liip.ch 一句话脚本

这三种方案都是可行的，而且方便程度逐个递增。
但是， Apple 在`macOS10.11`引入了「Rootless机制」，简单来说就是 root 用户也无法操作某些系统关键部位，例如 php 的目录 `/usr/bin`，
详见[wwdc相关内容](https://developer.apple.com/videos/play/wwdc2015/706/)。

因此，以上三种方式暂时行不通，他们无法修改 php的默认指向，解决方案有二：

-  使用第三方集成环境，e.g. `mamp` / `xampp`
-  关闭`Rootless机制`，然后自己配置环境

第一种方式我没实操，但是据说`mamp`之类软件兼容性也不怎么样。。。
我采用了第二种方式，


关闭Rootless机制的方法为：
```
//重启按住 Command+R，进入恢复模式，打开Terminal。

csrutil disable
```

修改好了之后还可以选择再次打开，打开的方式为：

```
//重启按住 Command+R，进入恢复模式，打开Terminal。

csrutil enable
```
好，现在你已经成功的把Rootless机制给关掉了，然后就可以从以下三种方式中选择一种了

-  重新编译 php
-  用 brew 安装一个新的 php
-  https://php-osx.liip.ch 一句话脚本

重新编译PHP，可以参考我的另一篇博客：
  
后两种我都试过，macOS 10.11 ~ macOS 10.13 都可以用，

不过 macOS 10.12 之后，使用`brew`安装`php`时，可能会遇到`brew`缺少权限的问题，解决方案参考我的另一篇博客：
[mac brew 权限问题解决记录](http://blog.ericli.top/2018/01/18/mac%20brew%20权限问题解决记录的副本%205/)

一句话脚本，放在了AWS，使用的时候最好科学上网。

EOF


 