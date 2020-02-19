---
title: Ubuntu配置vpn(pptp)
date: 2015-12-07
tags: Linux
description: 本文记录了笔者在ubuntu云主机搭建vpn服务的过程。
---

1.pptp
======

>  -  PPTP（Point to Point TunnelingProtocol），即点对点隧道协议。该协议是在PPP协议的基础上开发的一种新的增强型安全协议，支持多协议虚拟专用网（VPN），可以通过密码验证协议（PAP）、可扩展认证协议（EAP）等方法增强安全性。可以使远程用户通过拨入ISP、通过直接连接Internet或其他网络安全地访问企业网。
>  - 默认端口：1723

1.安装pptpd服务
-----------

```
sudo apt-get install pptpd 
```

2.ip相关配置
--------
配置文件为/etc/pptpd.conf
可以使用vi对其编辑
进行本地ip和远程ip配置
```
sudo vi /etc/pptpd.conf 
```
将
```
# (Recommended)
#localip 192.168.0.1                             #取消注释
#remoteip 192.168.0.234-238,192.168.0.245        #取消注释
# or
#localip 192.168.0.234-238,192.168.0.245
#remoteip 192.168.1.234-238,192.168.1.245
```
修改为

```
# (Recommended)
local 192.168.0.1                           
remoteip 192.168.0.234-238,192.168.0.245
# or
#localip 192.168.0.234-238,192.168.0.245
#remoteip 192.168.1.234-238,192.168.1.245
```
备注：
localip是linux主机的ip地址，remoteip是地址池，也就是远程拨进来的计算机所分配到的ip地址。此配置不唯一，可根据需求自行修改。

3.配置dns
-------
配置文件为/etc/ppp/pptpd-options
可用vi对其修改，配置dns

```
sudo vi /etc/ppp/pptpd-options
```
将

```
# Network and Routing

# If pppd is acting as a server for Microsoft Windows clients, this
# option allows pppd to supply one or two DNS (Domain Name Server)
# addresses to the clients.  The first instance of this option
# specifies the primary DNS address; the second instance (if given)
# specifies the secondary DNS address.
# Attention! This information may not be taken into account by a Windows
# client. See KB311218 in Microsoft's knowledge base for more information.
#ms-dns 10.0.0.1
#ms-dns 10.0.0.2
```
修改最后两行或新增配置信息

```
# Network and Routing

# If pppd is acting as a server for Microsoft Windows clients, this
# option allows pppd to supply one or two DNS (Domain Name Server)
# addresses to the clients.  The first instance of this option
# specifies the primary DNS address; the second instance (if given)
# specifies the secondary DNS address.
# Attention! This information may not be taken into account by a Windows
# client. See KB311218 in Microsoft's knowledge base for more information.
ms-dns 192.168.0.1    #可以是之前配置的localip
ms-dns 8.8.8.8        #可以是dns服务器的ip，例如8.8.8.8，8.8.4.4，114.114.114.114。前两者为google提供，最后一个是国内的，可根据需求进行相应配置。
```

4.设置vpn登录账号
-----------
配置文件为/etc/ppp/chap-secrets
可用vi对其进行修改
进行账号相关操作

```
sudo vi /etc/ppp/chap-secrets
```
将
```
# Secrets for authentication using CHAP
# client	server	secret			IP addresses

```
修改为

```
# Secrets for authentication using CHAP
# client	server	secret			IP addresses
user1	*	password1	*
user2	*	password2	*
```

配置操作如下

```
#用户名（tab）主机名（tab）密码（tab）分配的ip

例如：

user	*	password	*

#添加多项则设置多个登陆账号
```

 - client  -------------------用户名
 - server  ------------------主机名，*代表自动获取
 - secret  ------------------密码
 - IP addresses ----------分配的ip，*代表自动分配

5.重启pptpd
---------

```
sudo service pptpd restart
```

6.登录
----------------

 1. windows 和 osx 基本没有问题，记得选ppt（点对点隧道）
 2. linux记得打开"高级"，选中"Use Point-toPoint encryption (MPPE)"

7.访问外网失败的解决方案
-------------
此时原因是目的主机跟源主机不在同一网段，所以数据包暂时发往内网默认网关处理，而本网段的主机对此数据包不做任何回应。

打开 ipv4 forward 路由转发功能

配置文件为/etc/sysctl.conf

将

```
# Uncomment the next line to enable packet forwarding for IPv4
#net.ipv4.ip_forward=1         #取消注释
```
修改为

```
# Uncomment the next line to enable packet forwarding for IPv4
net.ipv4.ip_forward=1
```

> 关于ip forward的相关信息可参考[朝花惜时的博客](http://blog.sina.com.cn/s/blog_a94d887a01017b01.html)

