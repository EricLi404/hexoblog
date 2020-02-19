---
title: lamp配置（ubuntu sever）
date: 2015-04-26
tags: [Linux,PHP,Apache,MySQL]
description: 本文记录了在 ubuntu sever 配置 lamp 环境的过程。
---
1.apache2配置
===========

1.更新软件源
-------

```
sudo apt-get update
```

2.安装apache2及相关组件
----------------

```
sudo apt-get install apache2 apache2-bin apache2-data apache2-doc apache2-mpm-prefork apache2-utils
```

3.修改默认首页
--------

```
sudo vi /etc/apache2/mods-enabled/dir.conf
```
将
```
<IfModule mod_dir.c>
DirectoryIndex index.html index.cgi index.pl index.php index.xhtml index.htm 
</IfModule> 
```
修改为

```
<IfModule mod_dir.c>
DirectoryIndex index.php index.html index.cgi index.pl index.xhtml index.htm 
</IfModule> 
```

4.重启apache2服务
-------------

```
sudo service apache2 restart
```

打开

```
http://localhost
```
显示apache2相关信息，表明安装成功。

2.安装mysql
=========

1.安装mysql以及mysql-client
-----------------------

```
 sudo apt-get install mysql-server mysql-client
```
安装过程中会两次提示输入mysql密码

3.安装PHP
=======

1.安装php及相关拓展
------------

```

sudo apt-get install php5 php-pear php5-mysql php5-dev libapache2-mod-php5 php5-dev php5-curl php5-gd php5-imagick php5-mcrypt 
```

4.安装phpmyadmin
==============

1.安装phpmyadmin
--------------

```
sudo apt-get install phpmyadmin 
```
安装时要求我们选择版本支持，此时选择apache2即可，然后输入mysql的用户名以及密码。

2.在var/www/html里添加链接
--------------------

phpmyadmin默认安装位置是/usr/share/phpmyadmin,
我们应该在/var/www/html中建立/user/share/phpmyadmin的软链接，从而实现通过http://localhost/phpmyadmin访问phpmyadmin的效果

```
sudo ln -s /usr/share/phpmyadmin /var/www/html/phpmyadmin 
```

５． lamp开启方式
===========

 

```
 service apache2 start/restart/stop status 　 
 service mysql start/restart/stop status 　　　 
 mysql -u root -p 　　＃使用root用户登陆mysql,然后按提示输入密码。
```






