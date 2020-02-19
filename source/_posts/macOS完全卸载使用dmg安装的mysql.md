---
title: macOS完全卸载使用dmg安装的mysql
date: 2018-01-22
tags: [macOS,database,MySQL,brew]
description: 当年因为脑子进水了用mysql官网的dmg安装了mysql；事实证明，脑子进的水，早晚得自己一点一点排出来。
---

## 凤头

事情的起因是当我执行这段sql语句时报错：

```
CREATE TABLE `user` (
  `id` int(11) unsigned NOT NULL AUTO_INCREMENT, 
  `name` varchar(20) NOT NULL DEFAULT '' COMMENT '姓名',
  `email` varchar(30) NOT NULL DEFAULT '' COMMENT '邮箱',
  `is_admin` tinyint(1) NOT NULL DEFAULT '0' COMMENT '是否是超级管理员 1表示是 0 表示不是',
  `status` tinyint(1) NOT NULL DEFAULT '1' COMMENT '状态 1：有效 0：无效',
  `updated_time` timestamp NOT NULL DEFAULT '0000-00-00 00:00:00' COMMENT '最后一次更新时间',
  `created_time` timestamp NOT NULL DEFAULT '0000-00-00 00:00:00' COMMENT '插入时间',
  PRIMARY KEY (`id`),
  KEY `idx_email` (`email`)
) ENGINE=InnoDB  DEFAULT CHARSET=utf8 COMMENT='用户表';
```

报错的原因是`timestamp` 在目前版本默认不支持设置`0000-00-00 00:00:00` 为默认值。

-------

## 猪肚

### 改默认时间

我在慕课网相关评论里边看到，只要把默认时间设置为`1997-01-01 00:00:00` 就可以了。

尝试>>>>>>>>>>>>>>>>>>>>>>失败

### 改配置文件`my.cnf`

据说可以修改`/etc/my.cnf` 中的`sql_mode` 使`timestamp` 支持设置`0000-00-00 00:00:00` 为默认值。

```
sql_mode=STRICT_TRANS_TABLES,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION
```

然而我却发现： 我并没有 `/etc/my.cnf` .....

于是，将`/usr/local/mysql/support-files/my-default.cnf` 复制到 `/etc/my.cnf`

并配置

```
sql_mode=STRICT_TRANS_TABLES,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION
```

然后进入系统设置，重启 mysql ，结果.....

-------------

## 豹尾

接下来我就开始准备卸载 mysql ，转用 brew 重装 mysql

### 删除 mysql 管理程序
进入 「系统偏好设置」 右键点 「mysql」 删除之。

### ～～擦屁股～～ 删除 mysql 残留文件

```
sudo rm -rf /usr/local/mysql
sudo rm -rf /usr/local/opt/mysql
sudo rm -rf /usr/local/share/mysql
sudo rm -rf /usr/local/var/mysql
sudo rm -rf /usr/local/include/mysql
sudo rm -rf /usr/local/Cellar/mysql
sudo rm -rf /usr/local/bin/mysql
            
```
大概，，，删除完了吧。。。CleanMyMac 再清理一下吧，，，可怕