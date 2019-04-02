---
title: MySQL常用存储引擎
categories:
- MySql
tags:
- MySql
---
### MyISAM
#### 注释
1、MySQL 5.5 之前版本默认存储引擎
2、在排序、分组等操作中，当数量超过一定大小之后，由查询优化器建立临时表使用的存储引擎。

#### 存储结构
MyISAM 存储引擎表由 .MYD (表数据) 、.MYI (表索引) 与 .frm (表结构) 组成。

#### 特性
1、表级锁
2、表损坏修复
```shell
check table tableName
repair table tabelName
```
3、全文索引
4、数据压缩
```shell
myisampack
```

#### 容量
在 MySQL 版本小于 5.0 时，默认表大小为 4G，如存储大表则要修改表的 MAX_Rows 和 AVG_ROW_LENGTH 参数。
在MySQL版本大于5.0时，默认支持为 256TB。

#### 适用场景
1、非事务型应用
2、只读类应用
3、空间类的应用

### InnoDB
#### 注释
MySQL5.5 及之后版本默认存储引擎。

#### 存储结构
InnoDB 使用表空间进行数据存储
```shell
innodb_file_pre_table => on #建议此模式
# InnoDB 引擎的表将产生 tableName.frm（独立表空间） 和 tableName.ibd 文件
innodb_file_pre_table => off
# InnoDB 引擎的表将产生 ibdata*（共享表空间） 和 tableName.frm
```
区别：1、系统表空间无法简单的收缩系统文件大小，造成大量的空间浪费，并且会产生大量的磁盘碎片，从而降低系统的性能。2、独立表空间可以通过 optimize table 命令收缩系统文件。3、系统表空间会产生IO瓶颈。4、独立表空间可以同时向多个文件刷新数据。

#### 特性
1、事务性存储引擎
2、ACID特性
3、行级锁（行级锁可以最大程度的支持并发，行级锁是有存储引擎层实现）
4、InnoDB 状态检查
```shell
show engine innodb status
```

#### 适用场景
InnoDB适用于大多数 OLTP 应用

### CSV
#### 注释
文件系统存储

#### 存储结构
.csv 文件存储表内容
.csm 文件存储标的元数据如表状态和数据量
.frm 文件存储表结构信息

#### 特性
1、以 csv 格式进行数据存储
2、所有列必须都是不能为 null 的数据
3、不支持索引
4、可以对数据文件直接编辑

#### 适用场景
数据交换的中间表

### Archive
#### 特性
1、以 zlib 对表数据进行压缩，磁盘 I/O更少
2、数据存储在 tableName.ARZ 为后缀的文件中
3、只支持 insert 和 select 操作
4、只允许在自增 ID 列上加索引

#### 适用场景
日志和数据采集类应用

### Memory
#### 特性
1、数据保存在内存中
2、支持 hash 索引（等值查找快）和 Btree 索引（范围查找快）
3、所有字段都为固定长度，例如：varchar(10) = char(10)
4、不支持 BLOB/TEXT 等大字段
5、表级锁
6、最大大小由 max_heap_table_size 参数决定

#### 适用场景
1、查找或者是映射表（邮编和地区的对应表）
2、用于保存数据分析中产生的中间表
3、用于缓存周期性聚合数据的结果表

### Federated
#### 特性
1、提供了访问远程MySQL服务器上表的方法
2、本地不存储数据，数据全部放到远程服务器上
3、本地需要保存表结构和远程服务器的连接信息