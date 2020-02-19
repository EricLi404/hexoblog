---
title: Linux用gedit保存文件失败的解决办法
date: 2015-06-11
tags: Linux
description: 本文记录了Linux用gedit保存文件失败的解决办法。
---

1.首先给予文件777权限

```
sudo chmod -R 777 *
```

2.然后打开gedit的 编辑->首选项->编辑器 将“在保存前生成备份文件”取消勾选

