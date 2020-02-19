---
title: timestamp变成Xtamp的解决办法
date: 2016-10-11
tags: PHP
description: 本文记录了timestamp变成Xtamp的解决办法。
---

在`php`发送`json`数据时，传了一个`timestamp`,数据可以正常发送，但是当用浏览器直接输出时，却变成了`Xtamp`，原因及解决办法如下：

----
**原因:**

`&times`被解析，

**最简单的解决办法:**
将`timestamp`放到`json`数组的第一位，保证前边不会有&就可以了。
 
 
 