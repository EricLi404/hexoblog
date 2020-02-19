---
title: 解决Linux下sublime text3无法输入中文的情况
date: 2015-03-12
tags: Linux
description: 本文记录了 Linux 下 Sublime text3 无法输入中文的解决方案。
---

> 最近心血来潮，想在试试在linux上写php是什么感觉，但在使用sublimetext3时却发现无法正常输入中文，于是靠我大百度，各大论坛博客，解决了这个问题。
>
> 百度经验有解决方案，但是，编辑太马虎了。。。。。。好多地方的“-”写成了“_”，而且不是很详细，很多人挂在了没有c++编译环境上。
>
> 所以整理了我的解决方案，我的情况为ubuntu 15.10+apt-get安装的sublime text3，其他不保证成功。

> sublime text3安装方式：
> `sudo add-apt-repository ppa:webupd8team/sublime-text-3
> sudo apt-get update
> sudo apt-get install sublime-text-installer`

1.首先保证你的电脑有c++编译环境
------------------
如果没有，通过以下指令安装

```
sudo apt-get install build-essential
sudo apt-get install libgtk2.0-dev
```

2.在～目录新建一个名为sublime-imfix.c的文件
------------------------------

内容如下：

```
#include <gtk/gtkimcontext.h>

void gtk_im_context_set_client_window (GtkIMContext *context,

         GdkWindow    *window)

{

 GtkIMContextClass *klass;

 g_return_if_fail (GTK_IS_IM_CONTEXT (context));

 klass = GTK_IM_CONTEXT_GET_CLASS (context);

 if (klass->set_client_window)

   klass->set_client_window (context, window);

 g_object_set_data(G_OBJECT(context),"window",window);

 if(!GDK_IS_WINDOW (window))

   return;

 int width = gdk_window_get_width(window);

 int height = gdk_window_get_height(window);

 if(width != 0 && height !=0)

   gtk_im_context_focus_in(context);

}

```

3.将上述文件编译成共享库libsublime-imfix.so
--------------------------------

```
gcc -shared -o libsublime-imfix.so sublime-imfix.c `pkg-config --libs --cflags gtk+-2.0` -fPIC
```

4.将libsublime-imfix.so拷贝到sublime_text所在文件夹
------------------------------------------

```
sudo mv libsublime-imfix.so /opt/sublime_text/
```

5.修改文件/usr/bin/subl的内容
----------------------

```
sudo vi /usr/bin/subl
```

将

```
#!/bin/sh
exec /opt/sublime_text/sublime_text "$@"
```

修改为

```
#!/bin/sh
LD_PRELOAD=/opt/sublime_text/libsublime-imfix.so exec /opt/sublime_text/sublime_text "$@"
```

此时，在命令中执行 subl 将可以使用搜狗for linux的中文输入，但此时使用其他方式依然无法正常输入中文。

6.为了使用鼠标右键打开文件时能够使用中文输入，还需要修改文件sublime-text.desktop的内容。
-------------------------------------------------------

命令

```
sudo vi /usr/share/applications/sublime-text.desktop
```

将[Desktop Entry]中的字符串

```
Exec=/opt/sublime_text/sublime_text %F
```

修改为

```
Exec=bash -c "LD_PRELOAD=/opt/sublime_text/libsublime-imfix.so exec /opt/sublime_text/sublime_text %F"
```

将[Desktop Action Window]中的字符串

```
Exec=/opt/sublime_text/sublime_text -n
```

修改为

```
Exec=bash -c "LD_PRELOAD=/opt/sublime_text/libsublime-imfix.so exec /opt/sublime_text/sublime_text -n"
```

将[Desktop Action Document]中的字符串

```
Exec=/opt/sublime_text/sublime_text --command new_file
```

修改为

```
Exec=bash -c "LD_PRELOAD=/opt/sublime_text/libsublime-imfix.so exec /opt/sublime_text/sublime_text --command new_file"
```


> 注意： 修改时请注意双引号"",否则会导致不能打开带有空格文件名的文件。
>
> 此处仅修改了/usr/share/applications/sublime-text.desktop，但可以正常使用了。
>
> opt/sublime_text/目录下的sublime-text.desktop可以修改，也可不修改。






