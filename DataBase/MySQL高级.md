## MySQL高级

[TOC]

---

### MySQL安装及配置

* 检查是否安装过

  ```shell
  rpm -qa | grep mysql
  ```

  

* 为root设置密码

  ```shell
  /usr/bin/mysqladmin -u root password 123456
  ```

* 设置mysql自启

  ```shell
  systemctl is-enabled mysqld;
  # checkconfig --list | grep mysqld
  systemctl enable mysqld;
  # checkconfig mysqld on
  ```

* 安装位置

  | 位置               | 内容               | 备注                     |
  | ------------------ | ------------------ | ------------------------ |
  | /usr/bin/          | 相关命令           | mysqladmin , mysqldump等 |
  | /usr/share/mysql/  | 相关配置文件       | mysql.server命令及其配置 |
  | /var/lib/mysql/    | 数据库文件保存位置 | /etc/my.cnf   datadir    |
  | /etc/init.d/mysql/ | 启停脚本           |                          |

* 修改配置文件

  ```shell
  #先将默认配置复制到/etc/my.cnf
  # 5.5版本 cp /usr/share/mysql/my-huge.cnf /etc/my.cnf
  # 5.6版本 cp /usr/share/mysql/my-default.cnf /etc/my.cnf
  
  
  #修改字符集
  # 在数据库查看当前字符集 show variables like '%character%';
  ```

  

#### 配置文件 /etc/my.cnf

```shell
#---------------------------日志配置---------------------------
# log-bin 二进制日志文件  可用于主从复制
log-bin=/var/mysql/data/mysqlbin

#log-error  错误日志  默认关闭
#记录严重警告和错误信息 每次启动和关闭的信息
#log-error=/var/log/mysqld.log

#log 查询日志 默认关闭
#记录查询的SQL语句，开启会降低mysql性能
#log = /var/log/mysqllog

#---------------------------数据文件配置------------------------
#数据库文件保存路径
datadir=/var/lib/mysql
# MyISAM引擎：库目录下，frm文件存放表结构，myd文件存放表数据，myi文件存放表索引
# InnoDB引擎：库目录下，frm文件存放表结构，idb文件保存表数据和索引
```



#### MySQL逻辑架构

* 自顶向下

  ```shell
  1. 连接层
  2. 服务层：SQL编程接口->解析器(例如注释的处理)->SQL优化->缓存
  3. 数据存储引擎层： 可拔插组件式 存储引擎（ MYISAM,INNODB等）
  4. 数据存储层：文件系统，文件/日志
  ```



#### 存储引擎

```SQL
show engines;--显示支持的所有引擎
show variables like '%storage_engine%';--查看当前引擎和默认引擎
```



#### MyISAM 与 InnoDB对比

| 对比项 | MyISAM                                         | Innodb                                           |
| ------ | ---------------------------------------------- | ------------------------------------------------ |
| 主外键 | 不支持                                         | 支持                                             |
| 事务   | 不支持                                         | 支持                                             |
| 行表锁 | 表锁，操作一条记录也会锁住整个表，不适合高并发 | 行锁，只锁定一行，不会对其他行有影响，适合高并发 |
| 表空间 | 小                                             | 大                                               |
| 缓存   | 只缓存索引，不缓存真实数据                     | 缓存索引和真实数据，对内存要求高                 |
| 关注点 | 性能，偏向于读                                 | 事务                                             |





### 索引优化

#### SQL性能下降原因

SQL执行慢，执行时间长，等待时间长

* SQL写的太烂
* 索引失效
* 关联查询太多join
* 服务器参数设置



#### SQL执行顺序

* 手写顺序

  ```SQL
  select -> from -> join ->  where -> group by -> having -> order by -> limit
  ```

* 机器执行

  ```SQL
  from -> join -> where -> group by -> having -> select -> distinct -> order by -> limit
  ```

* 总结

  机器从FROM开始执行

#### Join查询

* 

  ```SQL
  -- 内连接 INNER JOIN 取A，B交集部分
  select * from A INNER JOIN B ON A.key = A.key;
  -- 左外连接 LEFT JOIN 取A独有，以及A与B的共有
  select * from A LEFT JOIN B ON A.key = B.key;
  -- 取A中独有的部分
  select * from A LEFT JOIN B ON A.key = B.key WHERE B.key IS NULL;
  -- 全外连接 FULL JOIN 取A，B独有及其交集
  -- select * from A FULL JOIN B ON A.key = B.key; -- MySQL不支持Full join
  select * from A LEFT JOIN B ON A.key = B.key --取A表和交集
  UNION
  select * from A RIGHT JOIN B ON A.key = B.key WHERE A.key IS NULL;-- 取B表独有
  -- 取A，B独有
  -- select * from A FULL JOIN B ON A.key = B.key WHERE A.key IS NULL OR B.key IS NULL; -- MySQL不支持，使用UNION来实现
  select * from A LEFT JOIN B ON A.key = B.key where B.key IS NULL --A独有
  UNION
  select * from A RIGHT JOIN B ON A.key = B.key where A.key IS NULL;-- B独有
  ```

  


#### 索引

索引（Index）是一种数据结构，其目的是为了提高查找效率(类比字典)。

两大功能：查找（where子句），排序（order by子句）

<font color="red">影响到where子句查找，和order by子句排序</font>

##### 排好序的快速查找的数据结构

索引以某种方式引用（指向）数据，没有特别说明索引一般是B树(多路搜索树)，唯一索引默认是B+树

![](..\resources\img\index.png)

##### 索引的优势与劣势

* 优势

  * 提高数据检索效率，降低数据库IO成本；

  * 通过索引对数据排序，降低数据排序成本，降低CPU消耗

* 缺点

  * 索引也是表，保存了主键和索引字段，并指向实体表记录，也要占用空间
  * 提高了查询效率，但是更新数据时也要更新索引信息
  * 需要根据实际情况不断优化索引

##### 索引类型

一张表的索引最好不要超过5个

```SQL
-- 单值索引，使用单个列作为索引
-- 唯一索引，索引列的值必须唯一，但允许多个为空
-- 复合索引，一个索引包含多个列
```



```SQL
-- 创建索引，索引名一般取 index_列名
CREATE [UNIQUE] INDEX 索引名 ON 表(列1[,列2...]);
ALTER TABLE 表名 ADD [UNIQUE] INDEX 索引名 ON (列1[,..]);
-- 添加主键就是添加唯一非空索引
-- 删除索引
DROP INDEX 索引名 ON 表;
-- 查看索引
SHOW INDEX FROM 表;
```



* 需要建立索引的情况
  * 主键自动建立唯一索引
  * 频繁作为查询条件的字段
  * 与其他表关联的字段
  * 高并发情况下适用符合索引
  * 排序字段
  * 统计或分组字段
* 不使用索引的情况
  * 表记录太少
  * 频繁更新的字段
  * where子句中用不到的字段
  * 数据重复的字段



### 性能分析

* MySQL Query Optimizer 优化器  负责将SQL按照MySQL的优化策略进行优化
* MySQL性能瓶颈：CPU，IO，服务器硬件性能

#### Explain

执行计划，使用EXPLAIN关键字可以模拟优化器执行SQL，从而看到SQL最终执行顺序

```SQL
-- 使用方法
EXPLAIN SQL语句
```



* explain作用/执行结果

```shell
表的读取顺序：id ，table字段
操作类型：select_type字段
```



```SQL
id -- 表示操作表的顺序 
--id相同:从上往下执行，!!id不同：id越大越先执行, 
-- 有相同又有不同:大id先执行，相同id从上往下执行

select_type -- 查询类型
-- simple：简单查询，primary：外层查询，subQuery:子查询，
-- derived：衍生表 from中的子查询，union：联合查询 ，union result：联合查询结果

table -- 表名
-- <derived + id> 衍生表
partitions

type -- 访问类型 ，优化至少要达到range级别，最好reg级别
-- 最好到最差：system > const > eq_ref > ref > range > index >ALL
-- All:全表扫描件
-- const:常量，索引一次就能找到，用户比较primary key 和 unique索引
possible_keys
key
key_len
ref 
rows
filtered
Extra
```





