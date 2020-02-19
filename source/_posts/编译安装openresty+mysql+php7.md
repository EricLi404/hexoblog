---
title: CentOS编译安装openresty+mysql+php7
date: 2016-01-07
tags: [PHP,database,MySQL]
description: 本文记录了笔者在CentOS编译安装openresty+mysql+php7的过程。
---

# 1.准备
### 1.更新系统

```
yum makecache && yum -y update
```

### 2.安装常用基础软件

```
yum -y install bash-completion vim net-tools bind-utils wget screen
```
最后别忘了重启
### 3.需要下载的源码
 
>#### 1.openresty

```
wget https://openresty.org/download/ngx_openresty-1.9.7.1.tar.gz
```
>#### 2.mariaDB

```
wget http://sfo1.mirrors.digitalocean.com/mariadb/mariadb-5.5.47/source/mariadb-5.5.47.tar.gz
```

>#### 3.php7

```
wget -c --no-check-certificate -O php7-src-master.zip https://github.com/php/php-src/archive/master.zip
```


# 2.openresty
>### 1.依赖关系

openresty依赖于perl 5.6.1+, libreadline, libpcre, libssl，首先安装依赖关系。

```
yum install readline-devel pcre-devel openssl-devel gcc
```
>### 2.下载源码

使用wget下载

```
wget https://openresty.org/download/ngx_openresty-1.9.7.1.tar.gz
```
博主安装时最新版为1.9.7.1，可以登录http://openresty.org查看最新版本，选择需要的版本进行下载。<br><br>
解压下载好的压缩包

```
tar xzvf ngx_openresty-1.9.7.1.tar.gz
```

注意将1.9.7.1替换为你下载的版本号
>### 3.       ./configure

进入 ngx_openresty-VERSION/ 目录, 然后输入以下命令配置:

```
./configure
```

默认, --prefix=/usr/local/openresty 程序会被安装到/usr/local/openresty目录。
我们可以指定各种选项，比如	

```
./configure --prefix=/opt/openresty \
            --with-luajit \
            --without-http_redis2_module \
            --with-http_iconv_module \
            --with-http_postgres_module
```

具体用法参考./configure --help 或者官方文档
>### 4.编译、安装

使用make编译

```
make
```

安装

```
make install
```
>### 5.将nginx添加到系统变量

```
PATH=/usr/local/openresty/nginx/sbin:$PATH
export PATH
```
>### 6.默认项目路径

```
/usr/local/openresty/nginx/html
```
>### 7.启动、关闭、重启nginx

```
nginx -c /usr/local/openresty/nginx/conf/nginx.conf   //启动并加载配置文件
nginx  -s stop    //停止
nginx  -s reload    //重启
```


# 3.mariaDB
>### 1.安装依赖库、编译工具

```	
yum install -y apr* autoconf automake bison bzip2 bzip2* cloog-ppl cmake compat* cpp curl curl-devel fontconfig fontconfig-devel freetype freetype* freetype-devel gcc gcc-c++ gtk+-devel gd gettext gettext-devel glibc kernel kernel-headers keyutils keyutils-libs-devel krb5-devel libcom_err-devel libpng libpng-devel libjpeg* libsepol-devel libselinux-devel libstdc++-devel libtool* libgomp libxml2 libxml2-devel libXpm* libtiff libtiff* make mpfr ncurses* ntp openssl openssl-devel patch pcre-devel perl php-common php-gd policycoreutils telnet t1lib t1lib* nasm nasm* zlib-devel gd-devel 
```
>### 2.创建数据库文件夹，用户组

```
groupadd mysql && useradd -g mysql mysql -s /bin/false &&
mkdir -p /data/mysql &&
chown -R mysql:mysql /data/mysql &&
mkdir -p /usr/local/mysql
```
>### 3.下载mariaDB 5.5.47

```
wget http://sfo1.mirrors.digitalocean.com/mariadb/mariadb-5.5.47/source/mariadb-5.5.47.tar.gz
```
解压
```
tar zxf mariadb-5.5.47.tar.gz
```
进入目录
```
cd mariadb-5.5.47
```
>### 4.编译、安装mariaDB

```
cmake . -DCMAKE_INSTALL_PREFIX=/usr/local/mysql -DMYSQL_DATADIR=/data/mysql -DSYSCONFDIR=/etc &&
make && make install
```
check完之后没什么事可以去喝个茶，博主低配云主机编译了大概12分钟
>### 5.删除系统自带的mariaDB配置文件

```
mv /etc/my.cnf /etc/my.cnf.bak
```
>### 6.初始化数据库，配置启动脚本

```
cd /usr/local/mysql &&
./scripts/mysql_install_db --user=mysql --basedir=/usr/local/mysql --datadir=/data/mysql &&
ln -s /usr/local/mysql/my.cnf /etc/my.cnf &&
cp ./support-files/mysql.server /etc/rc.d/init.d/mysqld &&
chmod 755 /etc/init.d/mysqld &&
chkconfig mysqld on
```

>### 7.配置mariaDB启动脚本

```
vim /etc/rc.d/init.d/mysqld
```
改动如下：

```
basedir=/usr/local/mysql #MySQL程序安装路径
datadir=/data/mysql #MySQl数据库存放目
```
然后重启mariaDB
```
service mysqld restart
```

>### 8.将mariaDB加入系统环境变量

```
echo 'export PATH=$PATH:/usr/local/mysql/bin'>>/etc/profile && source /etc/profile
```
>### 9.把MariaDB的库文件链接到系统默认的位置，在编译PHP等软件时可以不用指定其库文件地址

```
ln -s /usr/local/mysql/lib/mysql /usr/lib/mysql &&
ln -s /usr/local/mysql/include/mysql /usr/include/mysql &&
mkdir /var/lib/mysql &&
ln -s /tmp/mysql.sock /var/lib/mysql/mysql.sock
```
>### 10.设置root账户mariaDB密码

```
mysql_secure_installation
```
接下来屏幕会出现一系列代码，代码及操作记录如下

```
/usr/bin/mysql_secure_installation: line 379: find_mysql_client: command not found

NOTE: RUNNING ALL PARTS OF THIS SCRIPT IS RECOMMENDED FOR ALL MariaDB
SERVERS IN PRODUCTION USE! PLEASE READ EACH STEP CAREFULLY!

In order to log into MariaDB to secure it, we’ll need the current
password for the root user. If you’ve just installed MariaDB, and
you haven’t set the root password yet, the password will be blank,
so you should just press enter here.

Enter current password for root (enter for none):<– 回车
OK, successfully used password, moving on…

Setting the root password ensures that nobody can log into the MariaDB
root user without the proper authorisation.

Set root password? [Y/n] <– y 回车
New password: <– 设置用户名为root的mysql密码
Re-enter new password: <– 密码确认
Password updated successfully!
Reloading privilege tables..
… Success!

By default, a MariaDB installation has an anonymous user, allowing anyone
to log into MariaDB without having to have a user account created for
them. This is intended only for testing, and to make the installation
go a bit smoother. You should remove them before moving into a
production environment.

Remove anonymous users? [Y/n] <– y 回车
… Success!

Normally, root should only be allowed to connect from ‘localhost’. This
ensures that someone cannot guess at the root password from the network.

Disallow root login remotely? [Y/n] <– y 回车
… Success!

By default, MariaDB comes with a database named ‘test’ that anyone can
access. This is also intended only for testing, and should be removed
before moving into a production environment.

Remove test database and access to it? [Y/n] <– y 回车
- Dropping test database…
… Success!
- Removing privileges on test database…
… Success!

Reloading the privilege tables will ensure that all changes made so far
will take effect immediately.

Reload privilege tables now? [Y/n] <– y 回车
… Success!

Cleaning up…

All done! If you’ve completed all of the above steps, your MariaDB
installation should now be secure.

Thanks for using MariaDB!
```

至此，mariaDB设置完毕

# 4.php7

>### 1.下载php7

```
wget -c --no-check-certificate -O php7-src-master.zip https://github.com/php/php-src/archive/master.zip
```
解压
```
unzip -q php7-src-master.zip
```
进入目录
```
cd php-src-master
```
>### 2.安装依赖包

```
yum -y install libxml2 libxml2-devel openssl openssl-devel curl-devel libjpeg-devel libpng-devel freetype-devel libmcrypt-devel
```
>### 3.编译、安装

```
./buildconf
```

此时会生成configure文件，然后

```
export LD_LIBRARY_PATH=/usr/local/libgd/lib &&
./configure --prefix=/usr/local/php \
--with-config-file-path=/usr/local/php/etc \
--with-mysql=/usr/local/mysql \
--with-mysqli=/usr/local/mysql/bin/mysql_config \
--with-mysql-sock=/tmp/mysql.sock \
--with-pdo-mysql=/usr/local/mysql \
--with-gd \
--with-png-dir=/usr/local/libpng \
--with-jpeg-dir=/usr/local/jpeg \
--with-freetype-dir=/usr/local/freetype \
--with-xpm-dir=/usr/ \
--with-vpx-dir=/usr/local/libvpx/ \
--with-zlib-dir=/usr/local/zlib \
--with-t1lib=/usr/local/t1lib \
--with-iconv \
--enable-libxml \
--enable-xml \
--enable-bcmath \
--enable-shmop \
--enable-sysvsem \
--enable-inline-optimization \
--enable-opcache \
--enable-mbregex \
--enable-fpm \
--enable-mbstring \
--enable-ftp \
--enable-gd-native-ttf \
--with-openssl \
--enable-pcntl \
--enable-sockets \
--with-xmlrpc \
--enable-zip \
--enable-soap \
--without-pear \
--with-gettext \
--enable-session \
--with-mcrypt \
--with-curl \
--enable-exif \
--enable-ctype &&
make && make install
```
在check完之后没什么事再去喝个茶吧，博主大概也用了12分钟
<br>安装完成后提示信息

```
Build complete.
Don't forget to run 'make test'.

Installing shared extensions:     /usr/local/php/lib/php/extensions/no-debug-non-zts-20151012/
Installing PHP CLI binary:        /usr/local/php/bin/
Installing PHP CLI man page:      /usr/local/php/php/man/man1/
Installing PHP FPM binary:        /usr/local/php/sbin/
Installing PHP FPM config:        /usr/local/php/etc/
Installing PHP FPM man page:      /usr/local/php/php/man/man8/
Installing PHP FPM status page:      /usr/local/php/php/php/fpm/
Installing phpdbg binary:         /usr/local/php/bin/
Installing phpdbg man page:       /usr/local/php/php/man/man1/
Installing PHP CGI binary:        /usr/local/php/bin/
Installing PHP CGI man page:      /usr/local/php/php/man/man1/
Installing build environment:     /usr/local/php/lib/php/build/
Installing header files:          /usr/local/php/include/php/
Installing helper programs:       /usr/local/php/bin/
  program: phpize
  program: php-config
Installing man pages:             /usr/local/php/php/man/man1/
  page: phpize.1
  page: php-config.1
/root/down/php-src-master/build/shtool install -c ext/phar/phar.phar /usr/local/php/bin
ln -s -f phar.phar /usr/local/php/bin/phar
Installing PDO headers:          /usr/local/php/include/php/ext/pdo/
```
恭喜你，成功了

>### 4.复制php配置文件到安装目录：

```
cp php.ini-production /usr/local/php/etc/php.ini
```
>### 5.备份系统自带配置文件：

```
mv /etc/php.ini /etc/php.ini.bak
```
>### 6.添加软链接到 /etc目录：

```
ln -s /usr/local/php/etc/php.ini /etc/php.ini
```
>### 7.拷贝模板文件为php-fpm配置文件：

```
cp /usr/local/php/etc/php-fpm.conf.default  /usr/local/php/etc/php-fpm.conf
```
>### 8.添加软连接到 /etc目录：

```
ln -s /usr/local/php/etc/php-fpm.conf /etc/php-fpm.conf
```
<hr>
*4-8合并操作*

```
cp php.ini-production /usr/local/php/etc/php.ini &&
mv /etc/php.ini /etc/php.ini.bak &&
ln -s /usr/local/php/etc/php.ini /etc/php.ini &&
cp /usr/local/php/etc/php-fpm.conf.default  /usr/local/php/etc/php-fpm.conf &&
ln -s /usr/local/php/etc/php-fpm.conf /etc/php-fpm.conf
```
<hr>
>### 9.修改php配置文件

```
vim /usr/local/php/etc/php.ini
```
#### 1.将`disable_functions =` （禁用掉某些比较“危险”函数，大概在298行），改为

```
disable_functions = passthru,exec,system,chroot,scandir,chgrp,chown,shell_exec,proc_open,proc_get_status,ini_alter,ini_alter,ini_restore,dl,openlog,syslog,readlink,symlink,popepassthru,stream_socket_server,escapeshellcmd,dll,popen,disk_free_space,checkdnsrr,checkdnsrr,getservbyname,getservbyport,disk_total_space,posix_ctermid,posix_get_last_error,posix_getcwd, posix_getegid,posix_geteuid,posix_getgid, posix_getgrgid,posix_getgrnam,posix_getgroups,posix_getlogin,posix_getpgid,posix_getpgrp,posix_getpid, posix_getppid,posix_getpwnam,posix_getpwuid, posix_getrlimit, posix_getsid,posix_getuid,posix_isatty, posix_kill,posix_mkfifo,posix_setegid,posix_seteuid,posix_setgid, posix_setpgid,posix_setsid,posix_setuid,posix_strerror,posix_times,posix_ttyname,posix_uname
```
#### 2.将`expose_php = On`(禁止显示php版本的信息，大概在359行)，修改为

```
expose_php = Off 
```
#### 3.将`;date.timezone =`（大概在912行），修改为

```
date.timezone = Asia/Shanghai
```

#### 4.将

```
 ;short_open_tag
 ;  Default Value: On
 ;  Development Value: Off
 ;  Production Value: Off
```   
(支持php短标签，大概在151行)，修改为

``
short_open_tag
   Default Value: On
   Development Value: Off
   Production Value: Off
 ``

#### 5.将 `;opcache.enable=0` (支持opcode缓存，大概在1732行)，修改为

``` 
opcache.enable=1
```
#### 6.将`;opcache.enable_cli=0`(支持opcode缓存，大概在1735行)，修改为

```
opcache.enable_cli=1
```

然后配置opcode缓存，取消以下配置项的注释（从1732行向下）

```
opcache.memory_consumption=64
opcache.interned_strings_buffer=4
opcache.max_accelerated_files=2000
opcache.revalidate_freq=2
opcache.fast_shutdown=1
```


>### 10.修改php-fpm配置项

```
vim /usr/local/php/etc/php-fpm.conf 
```
取消注释

``` 
pid = run/php-fpm.pid
```
新增

```
user = www 
group = www
```
>### 11.拷贝php-fpm脚本到启动目录,给予执行权限, 设置开机启动

```
cp /usr/local/src/php-7.1.0/sapi/fpm/init.d.php-fpm /etc/rc.d/init.d/php-fpm &&
chmod +x /etc/rc.d/init.d/php-fpm &&
chkconfig php-fpm on
```


### 12.配置nginx支持php

```

location ~ \.php\$ {
root html;
fastcgi_pass 127.0.0.1:9000;
fastcgi_index index.php;
fastcgi_param SCRIPT_FILENAME /usr/local/openresty/nginx/html$fastcgi_script_name;
include fastcgi_params;
}
```

# 5.配置汇总
根目录： /usr/local/openresty/nginx/html/
Nginx配置文件： /usr/local/openresty/nginx/conf/nginx.conf
PHP配置文件： /usr/local/php/etc/php.ini
PHP默认拓展文件夹：/usr/lib64/php/modules/
PHP拓展可放置于：/usr/local/php/lib/php/extensions/no-debug-non-zts-20151012
MySQL配置文件： /etc/my.cnf
MySQL程序安装路径： basedir=/usr/local/mysql
MySQl数据库存放目录： datadir=/data/mysql





