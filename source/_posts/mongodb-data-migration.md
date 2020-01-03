---
title: mongodb数据迁移
categories:
  - coding
  - database
tags:
  - mongodb
date: 2020-01-03 11:27:31
update: 2020-01-03 11:27:31
---

这几天换服务器，然后在服务器上的`mongodb`就需要做一下数据迁移。在此记录下方法。

### 数据备份

在`mongodb`中使用`mongodump`命令来备份数据：
```bash
mongodump -h localhost:27017 -d test -o /path/to/mongobackups
```

<!-- more -->

**NOTE**: `test` 是要备份的数据库实例名称

### 数据还原

在`mongodb`中使用`mongorestore`命令来恢复备份数据：
```bash
mongorestore -h [remote host]:27017 -d test /path/to/mongobackups
```

**NOTE**: 如果要让`mongodb` 监听公网ip, 设置`/etc/mongod.conf` 中 `bindIp: 0.0.0.0`

## 参考
1. [MongoDB不同服务器之间的数据迁移](https://xiangsugar.github.io/2018/06/24/MongoDB%E4%B8%8D%E5%90%8C%E6%9C%8D%E5%8A%A1%E5%99%A8%E4%B9%8B%E9%97%B4%E7%9A%84%E6%95%B0%E6%8D%AE%E8%BF%81%E7%A7%BB/)