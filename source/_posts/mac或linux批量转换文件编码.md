---
title: mac或linux批量转换文件编码
date: 2016-12-15
tags: [macOS,Linux]
description: 批量转换文件编码 shell 脚本。
---

> 坑\*的 Android 老师，期末考试让我们在他给出的项目上继续写代码。。。然而，给出的项目中，java 代码部分用的是GB2312，其他地方用的都是 UTF-8，简直坑\*。



由于 Mac/Linux 自带一个 iconv ，可以转换文件编码，所以可以借用这个命令来进行文件编码的批量转换。


-----------------

**进入主题，，直接贴代码**

```
#!/bin/bash
find *.$1 -exec sh -c "iconv -f $2 -t $3 {} > {}.temp" \;
find *.$1 -exec rm {} \;
rename 's/\.temp$//' *.temp
```

**注意：**
有些版本的 osx 是不自带 rename 的，比如我目前（2016年12月）使用的osx10.12.2，需要自己安装rename 才可以使用，安装方式很简单，使用`brew install rename` 安装就可以了。

或者可以使用这个版本的代码：

```
#!/bin/bash

find *.$1 -exec sh -c "iconv -f $2 -t $3 {} > {}.temp" \;
find *.$1 -exec rm {} \;
for files in *.temp
do
     mv $files ${files%.temp}
done


```

**使用方式：**

按顺序传入三个参数：
要转码的文件拓展名  源编码   目的编码
e.g.
` alliconv.sh java gb2312 utf8 `

**ps：**
另外，macOS 上发现 `/usr/bin` ,`/usr/sbin` 目录不能写入，

此时可以：
1.新建一个运行目录
2.关闭 rootless

新建运行目录就不说了，一般就直接用 `/usr/local/bin`
下面给出关闭 rootless 的操作方式：

```
//重启按住 Command+R，进入恢复模式，打开Terminal。

csrutil disable
```

修改好了之后还可以选择再次打开，打开的方式为：

```
//重启按住 Command+R，进入恢复模式，打开Terminal。

csrutil enable
```

以上代码在 osx10.12中测试通过，linux 待测试。



