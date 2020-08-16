## MySQL高级

[TOC]

---

### 1.MySQL安装及配置

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

  

#### 1.1 配置文件 /etc/my.cnf

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



#### 1.2 MySQL逻辑架构

* 自顶向下

  ```shell
  1. 连接层
  2. 服务层：SQL编程接口->解析器(例如注释的处理)->SQL优化->缓存
  3. 数据存储引擎层： 可拔插组件式 存储引擎（ MYISAM,INNODB等）
  4. 数据存储层：文件系统，文件/日志
  ```



#### 1.3 存储引擎

```SQL
show engines;--显示支持的所有引擎
show variables like '%storage_engine%';--查看当前引擎和默认引擎
```



#### 1.4 MyISAM 与 InnoDB对比

**总结：**

> InnoDB：事务、行级锁、外键；存储结构：frm表结构文件+idb数据索引文件
>
> MyISAM：表锁级，不支持事务、外键访问快；存储结构：frm表结构文件+myd数据文件+myi索引文件

| 对比项 | MyISAM                                         | Innodb                                           |
| ------ | ---------------------------------------------- | ------------------------------------------------ |
| 主外键 | 不支持                                         | 支持                                             |
| 事务   | 不支持                                         | 支持                                             |
| 行表锁 | 表锁，操作一条记录也会锁住整个表，不适合高并发 | 行锁，只锁定一行，不会对其他行有影响，适合高并发 |
| 表空间 | 小                                             | 大                                               |
| 缓存   | 只缓存索引，不缓存真实数据                     | 缓存索引和真实数据，对内存要求高                 |
| 关注点 | 性能，偏向于读                                 | 事务                                             |





### 2. SQL性能下降原因

SQL执行慢，执行时间长，等待时间长

* SQL写的太烂
* 索引失效
* 关联查询太多join
* 服务器参数设置



### 3. SQL执行顺序

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

  

### 4. 索引

索引（Index）是一种数据结构，其目的是为了提高查找效率(类比字典)。

两大功能：查找（where子句），排序（order by子句）

<font color="red">影响到where子句查找，和order by子句排序</font>

**排好序的快速查找的数据结构**

索引以某种方式引用（指向）数据，没有特别说明索引一般是B树(多路搜索树)，唯一索引默认是B+树

![](..\resources\img\index.png)

#### 4.1  索引的优势与劣势

* 优势

  * 提高数据检索效率，降低数据库IO成本；

  * 通过索引对数据排序，降低数据排序成本，降低CPU消耗

* 缺点

  * 索引也是表，保存了主键和索引字段，并指向实体表记录，也要占用空间
  * 提高了查询效率，但是更新数据时也要更新索引信息
  * 需要根据实际情况不断优化索引

#### 4.2 索引类型

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

**尽量使用复合索引**

```sql
create index name_age_addr on users(name,age,addr);
相当于创建了三个索引：name、name+age、name+age+addr

而单列索引 name、age、addr，查询条件中同时存在三个索引列时，mysql只选择一个最优索引（辨识度最高）使用
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



### 4. 性能分析

* MySQL Query Optimizer 优化器  负责将SQL按照MySQL的优化策略进行优化
* MySQL性能瓶颈：CPU，IO，服务器硬件性能

#### 4.1  查看数据库SQL执行频率

```sql
show status like 'Com_______'; --显示当前连接执行的各种操作的行数
show global status like 'Com_______'; --显示整个数据库的操作的行数
show global status like 'Innodb_rows_%'; -- 显示整个innodb操作的行数
```

#### 4.2 定位低效SQL

* 慢查询日志：执行时长超过指定值时记录到慢查询日志
* show processlist：查看实时SQL执行情况

#### 4.3 Explain：查看SQL执行计划

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
id -- 表示操作表的优先级 
--id相同:从上往下执行，!!id不同：id越大越先执行, 
-- 有相同又有不同:大id先执行，相同id从上往下执行
---------------------------
select_type -- 查询类型,效率逐渐降低
-- simple：简单查询，
-- primary：外层查询，
-- subQuery:子查询，
-- derived：衍生表/临时表 from中的子查询，
-- union：联合查询 ，
-- union result：联合查询结果
---------------------------
table -- 该行查询结果的来源表
-- <derived + id> 衍生表
partitions
---------------------------TYPE
type -- 访问类型 ，优化至少要达到range级别，最好ref级别
-- 最好到最差：
-- null ：不查询表 如select now();
-- system：表中只有一条记录
-- const：常量，索引一次就能找到，用于比较primary key 和             unique索引
-- eq_ref：使用唯一索引进行多表关联
-- ref ：根据非唯一索引进行查询
-- range ：范围查询，where后出现 between ,<,>,in等
-- index ：遍历整个索引树
-- All:全表扫描件
---------------------------KEY
possible_keys -- 可能用到的索引
key -- 实际用到的索引，关注点：有没有使用索引
key_len -- 索引长度，越小越好
ref -- 引用
rows -- 需要扫描的行数，有索引时一般扫描1行
filtered -- 过滤的行
Extra -- 额外信息
-- using filesort 对数据使用外部索引排序，而不使用表内索引，需要优化
-- using temporary 使用了临时表，查询结果排序时使用临时表，常见于order by 和group by，需要优化
-- using index 使用了索引，避免访问数据行，效率不错
-- using where 使用索引查找的情况下，需要回表查询数据
-- using index condition 使用了索引，需要回表查询整行数据
-- using index;using where 使用了索引，所需数据均能在索引列中找到，无需会表查询
```



#### 4.5 show profile 分析SQL耗时

```sql
select @@have_profiling;-- 查看是否支持该操作
select @@profiling; -- 查看是否开启
set profiling=1; --开启该操作
```

```sql
show profiles; -- 显示最近执行的SQL语句的耗时情况：queryId，Duration(耗时),Query(语句)
show profile for query queryId; -- 显示指定查询语句的各执行阶段耗时
  -- Sending Data阶段 是mysql线程开始访问数据到把数据返回给客户端的阶段，该阶段最耗时
show profile cpu for query queryId;-- 显示该查询的CPU使用情况
show profile all for query queryId;-- 显示该查询的所有资源使用信息
```



#### 4.6 trance 分析优化器执行计划

```sql
set optimizer_trace="enabled=on",
end_markers_in_json=on; -- 开启
set optimizer_trace-max-men=100000; -- trace能够使用的内存大小

select * from users;-- 执行sql
select * from information_schema.optimizer_trace\G;-- 查看优化器信息
```



### 5. 索引的使用

#####  5.1 避免索引失效

*  全值匹配：对索引中的所有列都指定具体值，索引生效，执行效率高

```sql
create index name_age_addr on users(name,age,addr);-- 创建联合索引
select * from users where name="a" and age =18 and addr="xx"；-- 为索引的所有列指定值
```

* 最左前缀法则

对于复合索引，查询条件中必须有最左列，而且没有跳过中间列，如果跳过则只使用最左列索引

```sql
-- 符合索引 （name,age,addr）
-------- 走索引：查询条件中必须出现最左列索引
select * from users where name="a";
select * from users where name="a" and age =19;
select * from users where name ="a" and age =19 and addr="xx";
select * from users where age =19 and addr="xx" and name ="a";

-------- 不走索引 查询条件中不包含最左列索引
select * from users where age=19 and addr="xx";
select * from users where addr="xx";

-------- 跳过中间列 : 只走最左列索引，右侧索引无效
select * from users where name="a" and addr="xx";
```

* 范围查询之后的字段索引失效，in 后的索引不失效，not in 后的索引失效

```sql
-- 符合索引 （name,age,addr）
-- 以下查询只有 name,age索引有效
select * from users where name ="xx" and age > 10 and addr="xx";
select * from users where name ="xx" and age in (10,23) and addr="xx";--索引全部有效
select * from users where name ="xx" and age not in (10,23) and addr="x";-- addr索引失效
```

* 索引列上进行运算，索引失效

```sql
-- 符合索引（name,age,addr）
select * from users where substring(name,1,1) = 'a'; --索引失效
select * from users where name='a' and age=age-1;--age索引失效，name可用
```

* 字符串索引列不加单引号，索引失效

````sql
-- 符合索引（name,age,addr）
select * from users where name= a ; -- 索引失效
````

* 尽量使用覆盖索引(只查询索引字段)，避免使用select *

```sql
-- 符合索引（name,age,addr）
select * from users where name ="a"; -- explian 的extra显示 using index condition ,表示需要回表查询；
select name,age,addr from users where name = "a"; -- explain 的 Extra显示 using index;using where 表示 无需回表查询
select name,password
```

* or前使用索引，or后不使用索引，整个索引失效

* 索引的 like 匹配 条件 以 %开头 不走索引，可以使用覆盖索引来解决该问题

```sql
-- 符合索引（name,age,addr）
select * from users where name like "a%";--走name索引
select * from users where name like "%a%"; -- 不走索引
----- 覆盖索引
select age from users where name like "%a%"; --走 (name,age,addr)复合索引
```

* 如果Mysql评估发现使用索引比全表扫描慢，则会放弃使用索引

* is null 、is not null 可能走索引也可能不走

  ```sql
  字段中有大量非空时 is null 走索引、is not null 不走索引
  ```



##### 5.2 查看索引使用情况

```sql
show status like 'Handler_read%';
show global status like 'Handler_read%';
```



### 6. SQL优化

#### 6.1 大批量插入数据

* **load命令加载文件中的数据**

  ```sql
  表结构 user : id 主键,name,age,password
  ---------
  文件格式：/root/data.log
  1,"A",19,"xxx"
  2,"b",20,"xxx"
  3,......
  ---------
  导入数据
  load data local infile "/root/data.log" into table `user` fiels terminated by ',' lines ternimated by '\n'; -- 字段以,分隔，记录以换行分隔
  
  ----
  优化策略：
  1. 按照id主键顺序，有序导入效率高
  2. set unique_checks=0 ;--关闭唯一性校验，导入完成后再打开
  3. set autocommit=0; 关闭自动提交事务，数据全部导入完成后手动提交，再打开
  ```

#### 6.2 insert语句优化

* 将多条 insert into 合并，避免每次都要连接数据库

  ```sql
  insert into user values(null,'A',1),(null,"b",2),...;
  ```

* 手动提交事务

  ```sql
  set autocommit=0;
  start transaction;
  insert into users values(null.'a'.1),(null,'b',1),...;
  commit;
  set autocommit=1;
  ```

* 按照主键有序插入

#### 6.3 order by 优化

**两种排序方式：**

> filesort排序：通过返回的数据进行排序，效率低
>
> using index排序：通过索引直接返回有序数据

```sql
-- 索引：id主键索引，(name,age,addr)符合索引
```

* filesort 排序

  ```sql
  select * from users order by name; -- using filesort
  -- 返回的数据有的不在索引中
  ```

* 索引排序：要求使用覆盖索引

  ```sql
  select name,age,addr from users order by name;-- 覆盖索引
  select id,name from users order by name;
  -- 返回索引中的数据
  
  select name,age from users order by name,age;--索引排序
  --  索引字段排序要同时时asc或desc，而且排序顺序与索引顺序一致，否则不使用索引
  ```

  

