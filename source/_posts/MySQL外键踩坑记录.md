---
title: MySQL外键踩坑记录
date: 2018-01-22
tags: [MySQL,database]
description: 记录了使用MySQL外键时踩过的坑。
---


1. MySQL有两种常用的引擎类型：MyISAM和InnoDB。目前只有InnoDB引擎类型支持外键约束。
 
2. 外键的使用需要满足下列的条件：
 - 两张表必须都是InnoDB表，并且它们没有临时表。
 - 建立外键关系的对应列必须具有相似的InnoDB内部数据类型。
 - 建立外键关系的对应列必须建立了索引。
 - 假如显式的给出了CONSTRAINT symbol，那symbol在数据库中必须是唯一的。假如没有显式的给出，InnoDB会自动的创建。
   
3. 外键的四种动作介绍：（默认是RESTRICT）
 - CASCADE: 从父表中删除或更新对应的行，同时自动的删除或更新自表中匹配的行。ON DELETE CANSCADE和ON UPDATE CANSCADE都被InnoDB所支持。
 - SET NULL: 从父表中删除或更新对应的行，同时将子表中的外键列设为空。注意，这些在外键列没有被设为NOT NULL时才有效。ON DELETE SET NULL和ON UPDATE SET SET NULL都被InnoDB所支持。
 - NO ACTION: InnoDB拒绝删除或者更新父表。
 - RESTRICT: 拒绝删除或者更新父表。指定RESTRICT（或者NO ACTION）和忽略ON DELETE或者ON UPDATE选项的效果是一样的。
   
4. 外键的四种动作选择：
 - 父表更新时子表也更新（CASCADE），父表删除时如果子表有匹配的项，删除失败（RESTRICT）； 
 - 父表更新时子表也更新（CASCADE），父表删除时子表匹配的项也删除 （RESTRICT）。

5. `Cannot add foreign key constraint`, 这是创建外键时经常遇到的一个问题，排查方向：
 - 两边的类型属性等有出入（注意有些软件默认生成的id是有`unsigned` 的，注意观察两者类型是否一致）
 - 某个表里面已经有值了

