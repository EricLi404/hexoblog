---
title: mac使用 brew 安装php apache 及php nginx 记录
date: 2017-07-10 
tags: [brew,macOS,PHP,Apache,nginx]
description: 本文记录了笔者在使用 brew 安装 apache 和 nginx 时遇到的一些问题的解决方案。
---

## brew 安装php  apache bug
不管是 `PHP 5` 还是 `PHP7` 都会丢失`libphp5.so` 或者`libphp7.so` 导致无法使用。


**解决方案**

- 有人给作者的 Github 提了 issue ，解决方案见[https://github.com/Homebrew/homebrew-php/issues/3601](https://github.com/Homebrew/homebrew-php/issues/3601)
- stack overflow 也有相关讨论[https://stackoverflow.com/questions/38855651/php7-on-macos-sierra-beta-fails-to-install](https://stackoverflow.com/questions/38855651/php7-on-macos-sierra-beta-fails-to-install)

## brew 安装 php nginx bug

默认的 `nginx.conf` 配置得有一些问题。

`nginx.conf` 所在路径 `/usr/local/etc/nginx/nginx.conf` 

此配置文件中只涉及到默认的 php 路径，如果配置了 vhost ，该配置就不起作用了附上我的配置

```

#user  nobody;
worker_processes  1;

#error_log  logs/error.log;
#error_log  logs/error.log  notice;
#error_log  logs/error.log  info;

#pid        logs/nginx.pid;


events {
    worker_connections  1024;
}


http {
    include       mime.types;
    default_type  application/octet-stream;

    #log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
    #                  '$status $body_bytes_sent "$http_referer" '
    #                  '"$http_user_agent" "$http_x_forwarded_for"';

    #access_log  logs/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    #keepalive_timeout  0;
    keepalive_timeout  65;

    #gzip  on;

    server {
        listen       80;
        server_name  localhost;

        #charset koi8-r;

        #access_log  logs/host.access.log  main;

        location / {
            root   html;
            index index.php index.html index.htm;
        }

        #error_page  404              /404.html;

        # redirect server error pages to the static page /50x.html
        #
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }

        # proxy the PHP scripts to Apache listening on 127.0.0.1:80
        #
        #location ~ \.php$ {
        #    proxy_pass   http://127.0.0.1;
        #}

        # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
        #
        location ~ \.php$ {
	   fastcgi_intercept_errors on;
            root           html;
            fastcgi_pass   127.0.0.1:9000;
            fastcgi_index  index.php;
            fastcgi_param  SCRIPT_FILENAME  /usr/local/Cellar/nginx/1.12.0_1/html$fastcgi_script_name;
            include        /usr/local/etc/nginx/fastcgi_params;
        }

        # deny access to .htaccess files, if Apache's document root
        # concurs with nginx's one
        #
        #location ~ /\.ht {
        #    deny  all;
        #}
    }

    include servers/*;
}

```


我的 vhost 配置

```
	server {
	server_name cgadmin.com;
	listen 80 ;
        	root  /Users/leif/www/cgadmin/public;
        	index index.html index.htm index.php;
	location ~ .php$ {
        	fastcgi_pass 127.0.0.1:9000;
        	fastcgi_index index.php;
        	//这一句配置的是项目目录地址
        	fastcgi_param SCRIPT_FILENAME   /Users/leif/www/cgadmin/public$fastcgi_script_name;
        	include fastcgi_params;
   	 }
	}

```