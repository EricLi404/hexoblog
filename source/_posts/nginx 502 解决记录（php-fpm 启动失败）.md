---
title: nginx 502 解决记录（php-fpm 启动失败）
date: 2017-08-30
tags: [nginx,macOS,PHP]
description: 本文记录了升级 macOS 10.13 之后遇到 nginx 502 问题的解决方案。
---

安装 `macOS 10.13` 之后发现 `nginx` 产生了 `502 错误`，具体解决方式记录如下：

### 打开 `nginx error log`

> 注：
> 此处 nginx 使用的是 brew 安装的 nginx ，使用其他工具或不同时间安装的 nginx ，在查看 nginx error log 时具体操作可能不同。

我的`nginx`默认并没有打开 `error log`，编辑 `nginx.conf`，打开`error log`

```
error_log  logs/error.log;
```

此时，运行一下`sudo nginx` 会在`/usr/local/Cellar/nginx/1.12.0_1/logs` 下产生`error.log`  文件，

打开 error log 发现错误信息如下：

```
[error] 1676#0: *1 kevent() reported that connect() failed (61: Connection refused) while connecting to upstream, client: 127.0.0.1, server: www, request: "GET / HTTP/1.1", upstream: "fastcgi://127.0.0.1:9000", host: "www"
```
### 检查`php-fpm`

运行`sudo php-fpm` 报错如下：

```
[30-Aug-2017 14:33:37] ERROR: failed to open configuration file '/private/etc/php-fpm.conf': No such file or directory (2)
[30-Aug-2017 14:33:37] ERROR: failed to load configuration file '/private/etc/php-fpm.conf'
[30-Aug-2017 14:33:37] ERROR: FPM initialization failed
```

### 确定原因为`php-fpm` 配置文件丢失

在`/private/etc/` 找到了`php-fpm` 的默认配置文件`/private/etc/php-fpm.comf.default` ；

复制，新建一份配置文件：

```
sudo cp /private/etc/php-fpm.comf.default /private/etc/php-fpm.conf
```

再次运行`sudo php-fpm` 报错如下

```
/private/etc/php-fpm.conf
[30-Aug-2017 15:19:50] WARNING: Nothing matches the include pattern '/private/etc/php-fpm.d/*.conf' from /private/etc/php-fpm.conf at line 125.
[30-Aug-2017 15:19:50] ERROR: failed to open error_log (/usr/var/log/php-fpm.log): No such file or directory (2)
[30-Aug-2017 15:19:50] ERROR: failed to post process the configuration
[30-Aug-2017 15:19:50] ERROR: FPM initialization failed
```

进入`/private/etc/php-fpm.d` ，复制新建一份配置文件：

```
sudo cp /private/etc/php-fpm.d/www.conf.default /private/etc/php-fpm.d/www.conf
```

修改 `php-fpm log` 默认保存位置

编辑`sudo vim /private/etc/php-fpm.conf`

```
error_log = /usr/local/var/log/php-fpm.log
```

再次执行`sudo php-fpm`  ，无错误提示。

重新启动 `nginx`:

```
sudo nginx -s stop
sudo nginx
```
发现`502错误` 已解决




