---
title: 自用shell备份
date: 2018-01-24
tags: [macOS,shell,Linux]
description: 记录了我使用的一些便利性的shell脚本。
---

# 0x00 $path 设置
在 `/etc/paths` 或 `/etc/paths.d/*` 中配置即可。。

```
#我的 /etc/paths

/usr/local/bin
/usr/bin
/bin
/usr/sbin
/sbin
/usr/local/EricShell
```

配置完成后，需要重启终端，然后可以

```
# 输出环境变量
echo $path 
```




## 0X01 wechat-plugin 安装

WeChatPlugin-MacOS ： 微信小助手。

Github： [https://github.com/TKkk-iOSer/WeChatPlugin-MacOS](https://github.com/TKkk-iOSer/WeChatPlugin-MacOS)



```
#!/bin/zsh

# 需要 root 执行。。。。
#由于最近macOS微信频繁更新，且更新后微信小助手会失效，故写了个shell，用来安装微信插件

dldir="./WeChatPlugin-MacOS"
cd ~/Downloads

#判断是否下载过该插件，如果下载过则删除文件夹，重新下载最新版
if [ -d $dldir ]
then
	rm -rf ~/Downloads/WeChatPlugin-MacOS
fi
git clone https://github.com/TKkk-iOSer/WeChatPlugin-MacOS.git
./WeChatPlugin-MacOS/Other/Install.sh
rm -rf ~/Downloads/WeChatPlugin-MacOS
```

## 0x02 hexo-depoly

使用方式：

- hexo-depoly  # 本地部署 + Github部署 
- hexo-depoly server # 开启本地server
- hexo-depoly local  # 只在本地部署并开启本地server

**千万别用root用户执行。。。。。。**
*万一执行了。。。。删除 `public` 和`.deploy_git` 再换普通用户就可以了*
```
#!/bin/bash

cd ~/www/hexo/Droplet

if [[ $1 == "local" ]]
then
        cp _config_local.yml _config.yml
        hexo clean
        hexo g -d
        hexo d -g
        hexo server -p 8888
elif [[ $1 == "server" ]]
then
        hexo server -p 8888
else
         cp _config_all.yml _config.yml
         hexo clean
         hexo g -d
         hexo d -g
fi
```

> 此外，应特别注意：`if [ $1 == "local" ]` 这种写法在 `$1` 为空时会有问题，应使用 `if [[ $1 == "local" ]]` 。

## 0x03 nginx-logs-conclude  nginx日志文件汇总

```
#!/bin/bash

# 将 nginx 日志文件按日期整理归纳

base_path='/usr/local/Cellar/nginx/1.12.2_1/logs'

#  为了兼容 macOS , 使用 `data -r` 而不是用`data -d`获取日期

day=`date -r $(expr $(date '+%s') - 86400)  +"%Y-%m-%d"`
month=`date -r $(expr $(date '+%s') - 86400)  +"%Y-%m"`

mkdir -p $base_path/$month
mv $base_path/access.log $base_path/$month/access_$day.log

kill -USR1 `cat /usr/local/Cellar/nginx/1.12.2_1/logs/nginx.pid`
```




