---
title: Linux 笔记
date: 2015-12-18
tags: Linux
description: 本文记录了Linux学习过程中的一些笔记和错误。
---


1.ubuntu禁用错误报告
------

```
sudo vim /etc/default/apport
```
将`enabled 1`  改为`enable 0` 。

然后关闭apport服务

```
sudo service apport stop
```


2.Linux帮助
----
 - Linux的联机手册（man pages）存储位置为：/usr/share/man
 
3.目录的浏览、管理及维护
------
 - 自系统启动启动以来系统和进程的信息存储于：/var/run
 
 - 创建文件夹操作 mkdir a/b/c 报错  mkdir: a/b: No such file or directory ，解决方案为：mkdir -p a/b/c
 
 - 回到家目录的方法：
  
   -  cd 
    
   -  cd ~
   
   - cd $HOME
 - 计算文件或目录的容量
  - du [选项] [文件或目录名]
  -  -h  人性化显示；  -s仅显示总容量
	- e.g.   `du /root`
 - 显示文件的行、单词与字节统计信息
  -  wc  #依次#显示文件的行、单词数、字节的统计信息
  - -l 显示行数统计
  - -w 显示单词统计 
  -  -c 显示字节统计
 - 链接  软链接，硬链接（源文件删除后仍可使用）
  - ln -s /tmp/1.sh  /pmt/1.sh  (删除原文件后链接失效，软链接)
  - ln  /tmp/1.sh  /pmt/1.sh  (删除原文件后链接有效，硬链接)
 

4.文件的浏览
------

 - less 工具是对文件或其它输出进行分页显示的工具，应该说是linux正统查看文件内容的工具，功能极其强大。使用方式：less [参数]  文件

 - more命令，功能类似 cat ，cat命令是整个文件的内容从上到下显示在屏幕上。 more会以一页一页的显示方便使用者逐页阅读，而最基本的指令就是按空白键（space）就往下一页显示，按 b 键就会往回（back）一页显示，而且还有搜寻字串的功能 。more命令从前向后读取文件，因此在启动时就加载整个文件。
 
 - tail 查看文件尾部内容，默认显示10行
 
 - 可以使用file命令检测文件类型
 
5.Bash Shell
----
  > shell是一个命令解释器
  
  - Bourn shell（sh）是UNIX操作系统最原始的shell
  - Bourn-Again shell（Bash）由GNU项目开发，是标准Linux shell
  - Bash相比sh，增加了三个新特性
   - 文件名完成和命令历史
   - 为命令或程序起别名
   - 命令行编辑功能

6.输入/输出和管道（|）及相关命令
------
 - bash  shell保留了3个文件描述符：
  - 0           STDIN            标准输入
  - 1           STDOUT           标准输出
  - 2           STDERR           标准错误
 - 存放电子邮件内容的文件位于：/var/spool/mail
 - 输出重定向符为：>>

7.用户、群组和权限
----
 - /etc/passwd中存储了所有的用户信息，其中第二个字段关系到用户登录，如果第二位是“x”（osx下为“*”）则用户登录需要密码，如果为空，则不需要。 
 - **用户**密码存储在/etc/shadow文件中（DES加密）
 - **用户组**密码存储在/etc/gshadow文件中（DES加密）
 - chmod命令使用8进制
 - `chmod` 配置参数
    - `u` User，即文件或目录的拥有者；
    - `g` Group，即文件或目录的所属群组；
    - `o` Other，除了文件或目录拥有者或所属群组之外，其他用户皆属于这个范围；
    - `a` All，即全部的用户，包含拥有者，所属群组以及其他用户；
    - `r` 读取权限，数字代号为“4”;
    - `w` 写入权限，数字代号为“2”；
    - `x` 执行或切换权限，数字代号为“1”；
    - `-` 不具任何权限，数字代号为“0”；
    - `s` 特殊功能说明：变更文件或目录的权限。
 - 文件和目录的默认权限还需要经过掩码（umask）过滤掉某些不需要的默认权限，最后才能产生用户所需的文件和目录的最终权限。
  - 普通用户的默认掩码（umask）为：0002
  - root用户的默认掩码（umask）为：0022
  - 查看自己的掩码的命令为：umask
 - 在一个操作系统可以管理和维护一个硬盘上的文件系统之前，首先必须将这个硬盘分区，之后再把每一个分区格式化为文件系统。把一个分区格式化为文件系统就是将磁盘的这个分区划分成许多大小相等的小单元，并将这些小单元顺序的编号。这些小单元成为块（block），大小为4kb。
 - 磁盘被格式化成文件系统时，系统将自动生成一个i节点列表，每一个文件和目录都会对应于一个唯一的i节点，i节点的数量决定了在这个文件系统中最多可以存储多少个文件。  
 - 可以用ls的-i选项查看文件的i节点，操作例如：ls -il。
 - 可用df -ih查看所有i节点使用状态及各种详细信息。
 - cp命令与i节点表的关系：<br> 
   分配一个空闲的i节点并将这个新的i节点项（记录）放入i节点表中；创建一个新的目录项（记录）并将一个新的名字与i节点号相关联。
 - mv命令与i节点的关系：<br>
 	删除带有旧文件名的旧目录项（记录）；在i节点表上没有任何影响（除了时间戳）。
 - 硬链接不能跨越文件系统。

8.压缩与解压缩
------
 - 关于bzip2：
  - bzip2支持介质错误的恢复
  - bzip2可移植性好，可以工作在32位或64位的平台上
  - bzip2压缩比一般大于gzip
  - bzip2默认在创建一个新的压缩文件后不保存原有文件
  -  压缩命令 `bzip2 hello.sh`（压缩后文件名为hello.sh.bz2）
  - 解压命令 `bzip2 -d hello.sh.bz2`
 
 - gzip（gunzip的简写）能识别并压缩zip、gzip和compress
  -  压缩命令 `gzip hello.sh`（压缩后文件名为hello.sh.gz）
  - 解压命令 `gzip -d hello.sh.gz`

> bzip2和gzip都不可以对目录做打包压缩操作

- tar 
 -   -c创建打包文件
 -  --delete 从打包文件中删除
 - -r 追加文件至打包文档
 -  -t 列出打包文档的内容
 -  -x 释放打包文件
 -  -C指定解压路径
 -  -f 指定打包过后的文件名
 -  -j 打包后通过bzip2格式压缩
 -  -z 打包后通过gzip格式压缩
 -  -v 压缩过程中显示文件
 -  --remove-files 打包后删除源文件
 - e.g.
 - 将/etc打包到/tmp
   -  `tar -cvf /tmp/etc.tar /etc`仅打包，不压缩
   - `tar -zcvf /tmp/etc.tar.gz /etc` 打包后，压缩成gzip
   - `tar -jcvf /tmp/etc/tar.bz2 /etc` 打包后，压缩成bzip2 

 
9.vi编辑器
-----
 - 命令模式
  - e!放弃上一次写盘以来的变化
  - set number(或者set nu)添加行号
 - 拓展模式
  - ndd 删除n行
  - ndw 删除n个字符
  -  h  光标左移一位
  -  l   光标右移一位
  -  j   光标上移一行
  -  k   光标下移一行
  - gg  移动光标到首行
  - G  移动光标到尾行
  - nG  移动光标至n行
  - ^ 移至当前行首字符
  - $ 移至当前行尾字符
  - fx 移至下一个x字符处
  - Fx 移至上一个x字符处
  - w 右移一个单词
  - nw 右移n个单词
  - b  左移一个单词
  - nb 左移n个单词
  - u 撤销
  - yy  复制
  - p 粘贴至当前行之后
  - P粘贴至当前行之前
   
 - 多窗口编辑
  - :split 
  - 窗口的切换
  - ctrl+w+h跳到左边
  - ctrl+w+l跳到右边
  - ctrl+w+j跳到上边
  - ctrl+w+k跳到下边
 - 自动补全  ctrl+N  
  
10.硬盘
----
 - linux在SCSI硬盘最多可划分15个分区
 - linux在IDE硬盘最多可划分63个分区 

11.关机命令
-----
 
 ```
 halt
 init 0
 shutdown
 power off
 ```
12.进程
----
 - linux系统的free命令所显示的内存数量要比实际可获得的内存略少一点，这是因为计算机在工作期间，linux系统内核常驻在内存中，并且这些内存从来也不会释放。
 - init 进程永远是linux内核生成的第一个进程，进程id（PID）总是1.
 - kill命令
  - Linux中的kill命令用来终止指定的进程（terminate a process）的运行，是Linux下进程管理的常用命令。通常，终止一个前台进程可以使用Ctrl+C键，但是，对于一个后台进程就须用kill命令来终止，我们就需要先使用ps/pidof/pstree/top等工具获取进程PID，然后使用kill命令来杀掉该进程。kill命令是通过向进程发送指定的信号来结束相应进程的。在默认情况下，采用编号为15的TERM信号。TERM信号将终止所有不能捕获该信号的进程。对于那些可以捕获该信号的进程就要用编号为9的kill信号，强行“杀掉”该进程。 
  - 命令格式-------kill[参数][进程号]
  - 命令功能-------发送指定的信号到相应进程。不指定型号将发送SIGTERM（15）终止指定进程。如果任无法终止该程序可用“-KILL” 参数，其发送的信号为SIGKILL(9) ，将强制结束进程，使用ps命令或者jobs 命令可以查看进程号。root用户将影响用户的进程，非root用户只能影响自己的进程。
  - 命令参数<br>
  -l  信号，若果不加信号的编号参数，则使用“-l”参数会列出全部的信号名称<br>
-a  当处理当前进程时，不限制命令名和进程号的对应关系<br>
-p  指定kill 命令只打印相关进程的进程号，而不发送任何信号<br>
-s  指定发送信号<br>
-u  指定用户<br>
 


ubuntu server使用中遇到的错误
====
1.apache2
-----

> AH00558: apache2: Could not reliably determine the server's fully qualified domain name, using 127.0.0.1. Set the 'ServerName' directive globally to suppress this message

重启apache2时提示此错误，解决方法为：
在/etc/apache2/apache2.conf最后添加一行
` ServerName localhost:80`

 
