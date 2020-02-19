---
title: nginx笔记
date: 2018-01-25
tags: [macOS,Linux,shell]
description: 搞nginx的笔记
---

## 0x00. nginx.conf  主配置文件

```

#user  nobody;
# 有1个工作的子进程 ，一般可以设置为cpu核数
worker_processes  1;

# 打开error.log
error_log  logs/error.log;
#error_log  logs/error.log  notice;
#error_log  logs/error.log  info;

# 打开pid
pid        logs/nginx.pid;


events {
# 一个work_processes的最大连接数
    worker_connections  1024;
}


http { #http服务器
    include       mime.types;
    default_type  application/octet-stream;

	# 自定义的log格式
    log_format  ericlog '$remote_addr- "$request" '
                     '$status $body_bytes_sent "$http_referer" '
                        '"$http_user_agent" "$http_x_forwarded_for"';
    
    # 设置log文件名和log格式
    access_log  logs/access.log ericlog;

    sendfile        on;
    #tcp_nopush     on;

    #keepalive_timeout  0;
    keepalive_timeout  65;

    #gzip  on;

    server { #虚拟主机段
        #***
         location {  #定位,把特殊的路径或文件再次定位 ,如image目录单独处理,.php单独处理
          }             
    }
    include servers/*;
}


```

## 0x01. servers 虚拟主机配置

```

```


## 0x02. nginx-logs-conclude  nginx日志文件汇总shell脚本

```
#!/bin/bash

# 将 nginx 日志文件按日期整理归纳

base_path='/usr/local/Cellar/nginx/1.12.2_1/logs'

#  为了兼容 macOS , 使用 `data -r` 而不是用`data -d`获取日期

day=`date -r $(expr $(date '+%s') - 86400)  +"%Y-%m-%d"`
month=`date -r $(expr $(date '+%s') - 86400)  +"%Y-%m"`

mkdir -p $base_path/$month
mv $base_path/access.log $base_path/$month/access_$day.log
# 重读日志
kill -USR1 `cat /usr/local/Cellar/nginx/1.12.2_1/logs/nginx.pid`
```
## 0x03. 找不到 nginx.pid ？
    
在首次执行 `kill -USR1 $(cat /usr/local/Cellar/nginx/1.12.2_1/logs/nginx.pid)` 时，发现找不到 `nginx.pid` 。
实际去目录找了一下确实没有，此时原因是 `nginx.conf` 相关配置项没有开启。

此时一定要先关闭 nginx 
> 否则可能会报错 `[error] open() "/usr/local/Cellar/nginx/1.12.2_1/logs/nginx.pid" failed (2: No such file or directory)`

```
_ nginx -s stop
```
然后
编辑`nginx.conf` ,

```
#pid        logs/nginx.pid;
pid        logs/nginx.pid;
```
最后，指定配置文件启动 nginx

```
sudo nginx –c /usr/local/etc/nginx/nginx.conf
```

## 0x04. macOS crontab 定时任务使用

基础命令：
```
crontab -l  # list
crontab -e  # edit
```
任务定义：

|  *　|　 *  |　*　|　*　 |　*　|　command |
|:---:|:---:|:---:|:---:|:---:|:---:|
| 分 | 　时  |　日 |　月　| 周　| 命令 |

设置定时每天早上2:22汇总nginx的log文件

```
sudo crontab -e

#----------------------#

#在打开的文件中输入：
22 02 * * * /usr/local/EricShell/nginx-logs-conclude
```

## 0x05. location 语法

### 三种匹配方式

 - location = patt {}    #精准匹配
 - location patt{}       #一般匹配
 - location ~ patt{}     #正则匹配
 
### location 语法如何发挥作用

*以下内容摘自燕十八老师的课程讲义*

#### e.g.  1

```
  location = / {
              root   /var/www/html/;
             index  index.htm index.html;
  }
         
  location / {
             root   /usr/local/nginx/html;
            index  index.html index.htm;
  }

```
当访问`http://ericli.com/` 时，具体匹配流程为：
1. "/" 命中精准匹配 "/"，解析为`index` 的第一条配置`/index.htm` 。
2. "/index/htm" 命中一般匹配 ，

#### e.g.   2

```
location / {
            root   /usr/local/nginx/html;
            index  index.html index.htm;
        }

location ~ image {
           root /var/www/image;
           index index.html;
}

```
如果我们访问  `http://ericli.com/image/logo.png`
此时, "/" 与"/image/logo.png" 匹配
同时,"image"正则 与"image/logo.png"也能匹配, 
最终会访问正则匹配的 /var/www/image/logo.png 

#### e.g.   3

```
location / {
             root   /usr/local/nginx/html;
             index  index.html index.htm;
         }
 
location /foo {
            root /var/www/html;
             index index.html;
}
```
我们访问 `http://ericli.com/foo` ,对于uri "/foo",   两个location的patt,都能匹配他们,
此时, 真正访问后者的 `/var/www/html/index.html` 
原因:"/foo"匹配的更长(更具体)。


## 0x06. nginx 常用命令参数

**查看nginx相关进程**

```
ps aux|grep nginx
```

```
nginx -t            # 测试配置是否正确
nginx -s reload     # 加载最新配置
nginx -s stop       # 立即停止
nginx -s quit       # 优雅的停止
nginx -s reopen     # 重新打开日志
sudo nginx –c /usr/local/etc/nginx/nginx.conf     # 指定配置文件启动
```