---
title: Tips of macOS
date: 2017-06-14
tags: macOS
description: 本文记录了一些 macOS 的一些 tips 。
---
# 字体美化（华文黑体删除）
> 本章节参考了孫志貴的一篇博文
链接：http://www.jianshu.com/p/fa5515f09299

## 问题描述
1.很多第三方应用打开时会提示下载“华文黑体”，然而在 macOS10.13 中却总是下载失败；
2.macOS 中有很多字体在一些地方的显示效果会透露着一股农业重金属的感觉。

## 解决方案

在恢复模式的终端运行一个 shell , 删除相关字体。

### 创建 shell 脚本`a.sh`
在`/Volumes/Macintosh HD` 新建一个 shell 文件，内容在下面两个中选择一个。

```
只删除华文黑体

#!/bin/bash

cd "System"

find . -name "STHeiti*" -type f -delete
find . -name "华文*黑*" -type f -delete
find . -name "STXihei*" -type f -delete

```

```
删除所有不美观字体

#!/bin/bash

cd "System"


find . -name "STHeiti*" -type f -delete
find . -name "华文*黑*" -type f -delete
find . -name "STXihei*" -type f -delete
find . -name "Hei.ttf*" -type f -delete
find . -name "SIL-Hei-Med-Jian.fontinfo" -type f -delete
find . -name "儷黑 Pro.ttf*" -type f -delete
find . -name "LiHeiPro.fontinfo" -type f -delete
find . -name "BiauKai.ttf*" -type f -delete
find . -name "DFKaiShu-SB-Estd-BF.fontinfo" -type f -delete

```

### 运行 shell 脚本
1. 重启的时候按 cmd + R 进入恢复模式
2.  进入终端切换到相应目录，执行`bash a.sh`

### 注意
如果是删除全部不美观字体，那么耗时会比较长，我用了大概将近20分钟吧，要耐心等待，等他运行完再重启。


# 调整Launchpad行列数目
## 流程：

```
// 改变行数：
defaults write com.apple.dock springboard-rows -int X

// 改变列数：
defaults write com.apple.dock springboard-columns -int X

//改变生效：
killall Dock

其中X是大于0的整数。根据自己喜好调整即可。

```
恢复默认：
```
defaults write com.apple.dock springboard-rows Default
defaults write com.apple.dock springboard-columns Default
killall Dock
```
