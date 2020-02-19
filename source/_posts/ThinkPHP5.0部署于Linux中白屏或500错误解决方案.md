---
title: ThinkPHP5.0部署于Linux中白屏或500错误解决方案
date: 2017/03/29 12:58
tags: [PHP,Linux]
description: 部分 Linux 主机设置了 open_basedir（可将用户访问文件的活动范围限制在指定的区域，通常是入口文件根目录的路径） 选项，导致 ThinkPHP5 访问白屏或者报错。本文记录了作者解决该问题的过程。

---

## 问题描述：

>部分 Linux 主机设置了 open_basedir（可将用户访问文件的活动范围限制在指定的区域，通常是入口文件根目录的路径） 选项，导致 ThinkPHP5 访问白屏或者报错。

Safari 直接白屏，而 Chrome 报告了500错误，错误信息如下：

```
The www.xx.xom page isn’t working

www.xx.com is currently unable to handle this request.
HTTP ERROR 500
```
## 问题诊断

`ThinkPHP5.0` 官方手册称:
>如果把ThinkPHP5部署在了LAMP/LNMP环境上很有可能出现白屏的情况，这个时候需要开启 php 错误提示来判断是否是因为设置了open_basedir选项出错。

### 开启错误提示：

打开 `php.ini` 搜索 `display_errors`，把 `Off` 修改为 `On`就开启了 `php 错误提示` 。

*lnmp 的 php.ini 位置在`/usr/local/php/etc/php.ini`*

注意，要修改的是：

```
display_errors = Off
//修改为↓↓↓↓↓↓↓
display_errors = On
```
而不是：
```
; display_errors
;   Default Value: On
;   Development Value: On
;   Production Value: Off
```

这时再访问之前白屏的页面就会出现错误信息。如果错误信息如下那么很有可能就是因为`open_basedir`的问题。

```
arning: require(): open_basedir restriction in effect. File(/home/wwwroot/xx/thinkphp/start.php) is not within the allowed path(s): (/home/wwwroot/xx/public:/tmp/:/var/tmp/:/proc/) in /home/wwwroot/xx/public/index.php on line 17

Warning: require(/home/wwwroot/xx/thinkphp/start.php): failed to open stream: Operation not permitted in /home/wwwroot/xx/public/index.php on line 17

Fatal error: require(): Failed opening required '/home/wwwroot/xx/public/../thinkphp/start.php' (include_path='.:/usr/local/php/lib/php') in /home/wwwroot/xx/public/index.php on line 17
```

## 解决方案

### 首先尝试通过修改 php.ini解决

把权限作用域由入口文件目录修改为框架根目录
打开 `php.ini` 搜索 `open_basedir`,把

`open_basedir = "/home/wwwroot/xx/public/:/tmp/:/var/tmp/:/proc/"`
修改为

`open_basedir = "/home/wwwroot/xx/:/tmp/:/var/tmp/:/proc/"`
如果你的 `php.ini` 文件的 `open_basedir` 设置选项是被注释的或者为 `none`，那么你需要通过 `Apache` 或者 `Nginx` 来修改。

> 此处我的 `lamp1.3` 中`php.ini` 文件的 `open_basedir` 设置选项是被注释且为 `none`。
> 所以采用以下方案解决↓↓↓↓↓↓↓↓↓↓

### LAMP（Apache）修改方案
`Apache`需要修改 `httpd.conf` 或者同目录下的 `vhost` 目录下 `xx.conf` 文件，如果你的生成环境是 `LAMP一键安装包`配置,那么多半就是直接修改 `你的域名.conf` 文件

```
apache
├─vhost
    ├─www.thinkphp.cn.conf
    ├─......
├─httpd.conf
```
打开 `你的域名.conf`文件 搜索 `open_basedir`,把

`php_admin_value open_basedir "/home/wwwroot/www.thinkphp.cn/public/:/tmp/:/var/tmp/:/proc/"`
修改为

`php_admin_value open_basedir "/home/wwwroot/www.thinkphp.cn/:/tmp/:/var/tmp/:/proc/"`
然后重新启动 `apache` 即可生效

> 域名.conf 文件通常是在 /usr/local/apache/conf 目录中，当然了这取决于你 `LAMP` 环境配置

### LNMP(Nginx/Tengine) 修改方法

`Nginx` 需要修改 `nginx.conf` 或者 `conf/vhost` 目录下 `你的域名.conf` 文件，如果你的生成环境是 `LNMP/LTMP 一键安装包`配置那么多半就是直接修改 `你的域名.conf` 文件
```
nginx
├─conf
    ├─vhost
        ├─www.thinkphp.cn.conf
    ├─nginx.conf
    ├─......
├─nginx.conf
```
打开 `你的域名.conf` 文件 搜索 `open_basedir`,把

`fastcgi_param  PHP_VALUE  "open_basedir=/home/wwwroot/www.thinkphp.cn/public/:/tmp/:/proc/";`
修改为

`fastcgi_param  PHP_VALUE  "open_basedir=/home/wwwroot/www.thinkphp.cn/:/tmp/:/proc/";`
然后重新启动 `Nginx` 即可生效

>域名.conf 文件通常是在 /usr/local/nginx/conf/vhost 目录中，当然了这取决于你 LNMP/LTMP 环境配置
### fpm/fastcgi user.ini 修改方法

打开 项目根目录下找到 `user.ini` 文件，搜索 `open_basedir`,把

`open_basedir=/home/wwwroot/www.thinkphp.cn/public/:/tmp/:/proc/`
修改为

`open_basedir=/home/wwwroot/www.thinkphp.cn/:/tmp/:/proc/`
然后重新启动 `web 服务器` 即可生效



