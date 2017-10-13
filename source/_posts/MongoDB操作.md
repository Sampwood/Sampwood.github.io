---
title: MongoDB操作
date: 2017-10-12 10:12:17
categories:
- coding
- database
tags: mongodb
---

### 数据库操作

操作意图 | 方法
---|---
查看数据库 | show dbs
新建数据库 | use 数据库名==》然后创建表
删除当前数据库 | db.dropDatabase()
查看当前数据库所有表 | show collections

<!--more-->
### 表操作

操作意图 | 方法 
---|---
插入 | db.表名.insert({...})
保存 | db.表名.save({...})
修改 | db.表名.update({"条件字段名":"字段值"},{$set:{"要修改的字段名":"修改后的字段值"}})
删除 | db.表名.remove(条件);
查询所有数据 | db.表名.find()
按条件查询（支持多条件）| db.表名.find(条件)
查询第一条（支持条件）| db.表名.findOne(条件)
限制数量 | db.表名.find().limit(数量)
跳过指定数量 | db.表名.find().skip(数量)
统计记录条数 | db.表名.find().count()
限定返回的字段 | 