---
title: Ubuntu shadowsocks 服务器搭建
date: 2017-10-20
tags: [Linux,network]
description: 本文记录了作者在 Ubuntu 搭建 shadowsocks 服务器的过程。
---

## 一、一键安装脚本

https://teddysun.com/   提供了一键安装脚本

**使用方式：**
首先下载 shell 脚本，然后给执行权限，最后 sudo 执行脚本。
```
wget --no-check-certificate -O shadowsocks.sh https://raw.githubusercontent.com/teddysun/shadowsocks_install/master/shadowsocks.sh
chmod +x shadowsocks.sh
sudo ./shadowsocks.sh 2>&1 | tee shadowsocks.log
```
此时根据系统提示输入信息安装即可。

等到出现以下提示即为安装成功。
![这里写图片描述](http://img.blog.csdn.net/20171020152108121?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGVpZmx5eQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

**卸载方式：**

```
sudo ./shadowsocks.sh uninstall
```

此时如果需要修改配置文件，新增用户等操作，参考“二、手动安装”。





## 二、手动安装

更新软件源
```
sudo apt-get update
```

然后安装 PIP 环境
```
sudo apt-get install python-pip
```

安装 shadowsocks
```
sudo pip install shadowsocks
```
创建配置文件  并填充内容（见配置文件示例）：
```
sudo vim /etc/shadowsocks.json

```

单用户配置文件示例：
```
{
    "server":"0.0.0.0",
    "server_port":your_server_port,
    "local_address":"127.0.0.1",
    "local_port":1080,
    "password":"your_password",
    "timeout":300,
    "method":"your_encryption_method",
    "fast_open": false
}
```
多用户多端口配置文件示例：
```
{
    "server":"0.0.0.0",
    "local_address":"127.0.0.1",
    "local_port":1080,
    "port_password":{
         "8989":"password0",
         "9001":"password1",
         "9002":"password2",
         "9003":"password3",
         "9004":"password4"
    },
    "timeout":300,
    "method":"your_encryption_method",
    "fast_open": false
}
```

创建完毕后，赋予文件权限：
```
sudo chmod 755 /etc/shadowsocks.json
```

为了支持这些加密方式，你要需要安装

```
sudo apt–get install python–m2crypto
```


然后使用配置文件在后台运行：

```
sudo ssserver -c /etc/shadowsocks.json -d start
```

至此，配置完毕，Enjoy it.


----------


如果需要配置开机自启，则执行以下操作:

编辑 /etc/rc.local 文件
```
sudo vim /etc/rc.local
```
在 exit 0 这一行的上边加入如下

```
/usr/local/bin/ssserver –c /etc/shadowsocks.json
```

## 三、启动关闭指令

```
sudo /etc/init.d/shadowsocks stop
sudo /etc/init.d/shadowsocks start
```

