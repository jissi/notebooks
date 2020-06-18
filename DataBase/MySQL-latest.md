MySQL

[TOC]

---

## MySQL基础

### 相关概念

##### DB ：数据库，存储大量数据的仓库

##### DBMS ：数据库管理系统  MySQL就是一种DBMS；常见Oracle(甲骨文)/DB2(IBM)/SQL Server(微软)

DBMS分类：基于共享文件系统（Access数据库）；基于C/S架构（MySQL ，Oracle，SQL Server）

##### SQL：结构化查询语言，用来与数据库通信   ；



### 配置文件

Windows：安装路径下my.cnf

Linux：/etc/my.cnf

```shell
#客户端配置
[mysql]
#服务端配置
[mysqld]
port=3306
basedir=  #安装路径
datadir=  #数据保存路径
character-set-server=utf8 #字符集
default-storge-engine=INNODB #默认引擎
max-connections=100 #最大连接数
```



##### 登录命令

```shell
mysql -h localhost -P 3306 -u root -p
mysql --version #查看版本号 mysql -V
```



### 常见命令

```shell
select version();#查看当前mysql版本
show databases;#显示所有数据库
show engines;#查看支持的存储引擎/表类型
# mysql库用来保存用户信息，information_schema保存元数据信息
# performance_schema 存储性能参数
use Demo;#进入库
show tables;#显示库中的表
show tables from mysql;#直接显示库中的表
select databse();#显示当前所在数据库
desc tableName;#查看表结构
show index from 表名;#显示表中的索引
show variables like '%auto_increment%';#查看变量
set 变量名=值;#修改变量
```

### 语法规范

* 不区分大小写，建议关键字大写
* 命令以分号结尾
* 可以缩进，换行 ，建议关键字单独占一行
* 注释：单行```#注释```   ，单行 ```-- 注释 ```  ，多行 ```/*注释*/```

### DQL

数据查询语言 SELECT

#### 基础查询

```SQL
SELECT 查询列表 FROM 表名;
-- 查询列表：字段，常量，表达式，函数  
-- 查询结果是虚拟表
-- 字段与关键字相同时使用 `字段`
SELECT 100;SELECT 100%98;SELECT version();
-- 使用别名,可以不写AS，使用空格隔开
SELECT 100 [AS] result，100%98 [AS] 取模;
-- 别名与关键字重复时使用双引号，单引号也行
SELECT 100 AS "select";
-- 去重 distinct关键字
SELECT DISTINCT id FROM user;
-- 字符拼接 concat('a','b'[,...])  ，null 与任何字段拼接结果都为null
SELECT CONCAT(name,'age：',INNULL(age,20)) FROM user;
-- + 号的使用
'123'+10  --会尝试将字符转为数字运算，转换失败字符转为0
null + 123 --只要有一方为null，结果就是null
```

#### 条件查询 ：执行顺序：from  --> where --> select

```SQL
SELECT 查询列表 FROM 表名 WHERE 条件表达式
```

* 筛选条件分类

  1. 条件运算符：> , = , < , != , <> , >= , <=  , <=>安全等于可以判断null

  2. 逻辑运算符(用于连接条件表达式)：AND , OR , NOT      (也可以写成 && , || , ! )

  3. 模糊运算符：LIKE , IN  , BETWEEN AND ，IS [NOT] NULL

     * like ：%匹配 [0~n]个字符，_ 匹配单个字符 ；

       冲突时使用转义符：\\_ 、\\%     也可以声明某个字符表示转义

       ```SQL
       SELECT `name` FROM user WHERE `name` LIKE '_a_%' ESCAPE 'a';声明a为转义符\
       ```

     * between and：之间，包含临界值

       ```SQL
       SELECT * FROM user WHERE age >= 18 AND age <= 30;
       SELECT * FROM user WHERE age BETWEEN 18 ANd 30;
       ```

     * in：包含在(1,2,3)，取代多个OR

     * is null / is not null：为空

```SQL
SELECT * FROM user WHERE age != 18;-- 建议使用 <>
SELECT * FROM user WHERE NOT (`name`='a' AND  age > 18);-- 推荐 用关键字
SELECT * FROM user WHERE `name` LIKE '_a%' ESCAPE 'a';-- 指定a为转义符\  实际匹配 字%
SELECT * FROM user WHERE age BETWEEN 18 AND 30;-- >=80,<=30
SELECT * FROM user WHERE age IN(28,30);-- age=28 OR age=30
SELECT * FROM user WHERE name IS [NOT] NULL；-- =,<> 号不能判断null
```

#### 排序查询  order by 写在最后（除limit外）

```SQL
SELECT 查询列表 FROM 表名 WHERE 条件 ORDER BY 排序字段列表 ASC|DESC
-- 默认ASC
-- 排序字段支持 表达式，别名，函数
ORDER BY `name` ASC,age DESC;-- 先按name升序，如果顺序一样再按age降序排序
```

#### 常见函数

```SQL
SELECT 函数名(实参) [FROM 表 ...]
```

##### 1. 单行函数：做处理

* 字符函数

  ```SQL
  length("123"); -- 获取字节长度，utf-8中文占3字节
  concat('a','b'[,...]);-- 拼接多个字符,如果包含NUll结果为null
  upper('a')/lower('A'); -- 转大小写
  
  substr()/substring(); -- 截取
  --  整个SQL索引从1开始 ，之后一个字符索引为 -1
  substr("abc",1); -- 从索引1取到结束 abc ，两个参数截取之后所有
  substr("abc",1,2);-- 从索引1开始取2个 ，三个参数截取指定长度
  instr('abc','abcd');-- 字符串子串中第一次出现的索引，找不到返回0
  trim('  abc  ');-- 去掉前后空格
  trim('a' FROM 'abaca');-- 去掉前后a 返回 bac
  lpad("abc",5,'*'); -- 使用指定字符左填充到指定字节长度 **abc ，返回 指定长度 的子串
  rpad("abc",2,'*'); -- 返回 ab
  replace("abc",'a','d');-- 替换所有 返回dbc
  ```

  

* 数学函数

  ```SQL
  round(1.65); -- 四舍五入(绝对值)
  round(1.655,2);-- 四舍五入，保留小数点后两位
  ceil(1.65)/floor(1.65);-- 向上/下取整 返回>= / <= 的整数
  truncate(1.65,1);-- 区别于round,从小数点后截断 返回 1.6
  mod(100,98)；-- 取模/取余  SQL或JAVA中运算一样：mod(a,b) == a - a/b*b
  rand(); -- 产生[0-1)之间的随机小数
  ```

  

* 日期函数

  ```SQL
  -- mysql日期标准格式： 年-月-日
  now();--返回当前系统日期+时间
  curdate()/curtime();-- 仅返回当前系统日期/时间
  -- 年，月，日
  year(now())
  month(now())
  monthname(now());--月份名
  datediff(日期1，日期2)；-- 计算两日期差多少天
  str_to_date('2019*02*01','%Y*%m*%d');--安装指定格式将字符串解析为日期，2019-02-01
  date_fromat(now(),'%Y-%m-%d');-- 日期格式化为字符
  ```

  

* 其他函数

  ```SQL
  version(); -- 版本
  databse(); -- 当前数据库
  user();-- 当前用户
  ```

  

* 流程控制函数

  更多case信息参见控制流程<a href="#>>">>></a>

  ```SQL
  -- if else的效果 : 类似 if 条件?满足:不满足
  if(条件,返回满足,返回不满足);
  -- case既可以作为表达式嵌套在其他语句中，也可以作为独立语句放在begin end中使用
  -- 作为表达式使用返回值，不加分号；作为语句使用返回表达式，加分号
  -- 匹配到执行语句后就执行并跳出
  -- switch case效果  , then 返回值时不要写分号
  case 常量或表达式
  when 常量1 then 值/语句1 [；]
  when 常量2 then 值/语句2 [；]
  ...
  else 值/语句n[；]
  end [case;] -- 返回语句时使用 end case;
  /*	Java switch写法
  switch(常量或表达式){
  	case 常量1: 语句1;break; 
  	case 常量2: 语句2;break;
  	...
  	default:语句n;
  }
  */
  -- case用法1：作为表达式式使用
  SELECT 
      (
       CASE id
       when 1 then 1
       when 2 thtn 2
       else 3
       end
      ) as test 
  FROM user
  -- case用法2：作为语句使用 多重if  case后不跟条件
  case
  when age>18 then "18"
  when age>20 then "20"
  ...
  else "??"
  end [case;]
  ```

  

##### 2. 分组/聚合函数：做统计，忽略null值，可以和distinct搭配去重后运算

又称聚合函数，统计函数，组函数

```SQL
max() min() sum() count() avg()
SELECT max(id),min(id),sum(id),count(id),avg(id) FROM user;
-- 注意事项，全都忽略null值
sum()/avg() 一般传入数值参数
max()/min() 支持数值，字符，日期  （可排序）参数，
count() 支持所有类型参数，统计非空个数
-- 和聚合函数一起查询使用的字段要求是 group by 后的字段
-- 搭配distinct 去重后运算
-- count详细使用
count(*); -- 统计行数，只要不是整行为空就参与统计
count(1); -- 相当于在表中加了一列常量，然后统计常量出现次数
-- 效率
MYISAM 引擎下count(*)效率高，有内部计数器，直接返回
INNODB 引擎下count(*)与count(1)差不多单效率高于count(字段名)
```



#### 分组查询

注意：order by 在最后（除limit 外）

```SQL
SELECT 聚合函数列表/分组后字段 FROM 表名 [WHERE 条件] 
GROUP BY 字段名 [HAVING 条件]
[ORDER BY 排序列表]
-- 分组后筛选使用 having 条件
select count(*) from user group by type having count(*) > 2
-- 分组查询的筛选条件
分组前筛选，数据来自原始表 使用WHERE 放在 group by 之前
分组后筛选，数据来自分组后 使用Having 放在 group by 之后
-- 分组函数做筛选 条件一定在having中；能在分组前筛选的尽量使用where 提升效率
-- group by 和 having 支持别名，Oracle不支持

-- 按多个字段分组，没有顺序要求
group by type,age; -- 将type和age都相同的放在一组 
-- order by 用在最后对最终结果进行排序
```

#### 连接查询/多表查询

* JOIN 笛卡尔积 

  ```SQL
  SELECT * FROM A,B
  相当于用表A中每一条记录分别与表B匹配；导致所有行完全连接；最终结果 A表记录数 X B表记录数
  产生原因：没有有效的连接条件
  -- 添加条件避免笛卡尔积
  SELECT * FROM A,B WHERE A.id = B.id
  ```

* 连接查询分类

  * 按功能分

    - 内连接：等值连接，非等值连接，自连接

    - 外连接：左外连接，右外连接，全外连接

      * 左右外连接 LEFT/RIGHT JOIN

      ```shell
      分主表和从表，显示主表的所有记录，从表记录能与主表匹配就显示；
      一般用于查询一个表中有，另一表中没有的内容
      左右外连接 = 内连接 + 主表有而从表没有的数据
      ```

      - 全外连接 FULL JOIN

      ```SQL
      全外连接 = 内连接 + 主表独有记录 + 从表独有记录
      -- MySQL 不支持Full JOIn 可以使用 UNION 来实现
      ```

    - 交叉连接 CROSS JOIN

      笛卡尔积：等价于 SELECT * FROM A a,B b

  * 按年代分：

    * SQL92 ：支持内连接

      ```SQL
      -- 等值连接 A中每条记录与B中记录匹配，满足条件就显示，条件为=号，最终显示交集
      SELECT a.id,b.id FROM A a,B b WHERE a.id = b.id
      -- 注意：如果为表起了别名，查询字段就不能再使用原始表名 ：from 语句执行在前
      结果为多表的交集部分
      n表连接，至少要n-1个连接条件
      表的连接顺序没有要求
      一般要为表起别名
      可以组合 分组，排序，筛选等字句使用
      ```

      ```SQL
      -- 非等值连接：连接条件不是等于
      SELECT a.age , b.level FROM A a,B b WHERE a.age BETWEEN b.minAge AND b.maxAge
      ```

      ```SQL
      -- 自连接：自己连自己
      ```

      

    * SQL99：支持内连接，左右全外连接，交叉连接  ；提高可读性

      ```SQL
      SELECT 查询列表 FROM 表1 [连接类型] JOIN 表2  ON 连接条件 
      表3 [连接类型] JOIN 表4 ON [连接条件]
      [WHERE 筛选条件]
      [GROUP BY xx HAVING 条件] [ORDER BY xx]
      -- 连接类型
      [INNER] 内连接，默认
      LEFT [OUTER] 左外连接
      RIGHT [OUTER] 右外连接
      FULL [OUTER] 全外连接
      CROSS	交叉连接
      ```

      ```SQL
      -- 内连接
      SELECT * FROM A a [INNER] JOIN B b ON a.id = b.id
      -- 左/右外连接
      SELECT * FROM A a LEFT JOIN B b ON a.id = b.id
      -- 全外连接
      SELECT * FROM A a FULL JOIN B a ON a.id = b.id
      -- 交叉连接：笛卡尔积 相当于 A a,B b
      SELECT * FROM A a CROSS JOIN B b
      ```

      

#### 子查询

出现在其他语句内的select语句，称为子查询或内查询

* 分类

  * 按结果集行列数

    ```SQL
    标量/单行子查询：结果只有一行一列
    列子查询：结果一列多行
    行子查询：结果一/多行多列，一/多条记录
    表子查询：一般为多行多列
    ```

  * 按子查询出现位置

    ```SQL
    SELECT 后   -- 仅标量子查询
    FROM 后 -- 表子查询
    WHERE / HAVING 后 -- 标量/单行子查询，列子查询，行子查询
    Exists 后 -- 表子查询
    ```

* 应用

  1. where，having后

     ```SQL
     特点：
     放在()内，一般放在条件右侧，
     单行子查询一般搭配单行操作符( = ,> ,< ,...)
     列子查询一般搭配多行操作符使用
     (in/not in : 等于/不等于 列表中任意一个
       any/some : 与列表中的任意一个值比较：a > any(1,2,3) 等价于 a > min(xxx)
       all      : 与列表中所有值比较： a > all(1,2,3) 等价于 a > max(xxx)
     )
     ```

     ```SQL
     -- 标量/单行子查询
     SELECT * FROM A a WHERE a.id = (select id from b where id = 2);
     -- 列/多行子查询
     SELECT * FROM A a WHERE a.id = ANY(select id from B);
     等价于 select * from A a where a.id = select min(id) from B
     -- 行子查询 ：取代 where id = 子查询 AND age = 子查询  要求比较符相同
     SELECT * FROM A WHERE (id,age) = (SELECT MIN(id),MIN(age) FROM A)
     ```

  2. select 后  标量子查询（单行单列）

     ```SQL
     SELECT r.role_name,
     (SELECT COUNT(*) FROM  user u WHERE u.role_id = r.id) 人数
     FROM  role r
     ```

  3. from 后 表子查询,必须有别名！！！

     ```SQL
     SELECT COUNT(*) FROM 
     (SELECT user u INNER JOIN role r WHERE u.role_id = r.id) as t
     ```

  4. existes 后 表子查询  ，相关子查询

     ```SQL
     EXISTS 返回boolean类型的值，表子查询有结果就为1，没有返回0
     -- 语法
     EXISTS(子查询) -- 返回1,0
     SELECT EXISTS(SELECT * FROM user);
     SELECT id FROM user WHERE EXISTS(SELECT id FROM user WHERE id = 1);
     ```



#### 分页查询

limit(起始索引从0开始，显示记录数量)

执行顺序：

```SQL
from -> join -> on -> where -> group by -> having -> select -> order by -> limit
```

公式：page 页码，size个数  ：limit  (page-1)*size，size

```SQL
SELECT 查询列表 FROM 表名 [WHERE 条件] [GROUP BY 字段] [ORDER BY 字段] LIMIT 0,3

select * from user limit 0,2;-- 从索引0开始，显示两条记录
select * from user limit 2;-- 从索引0开始，显示两条记录
-- 显示第11条 - 第25条
limit 10,15
```



#### UNION联合查询

将多个查询结果合并成一个结果；要查询的结果来自多个表，且要查询的信息一样

```SQL
查询语句1 UNION 查询语句2 -- 默认去重
查询语句1 UNION ALL 查询语句2 -- 不去重
-- 注意事项
每个查询语句的 查询列数一致，
查询的每一列的类型和顺序一致
默认去掉重复项，使用 UNION ALL 可以包含重复项
```



### DML

数据操作语言：增 INSERT ，删 DELETE ，改 UPDATE

#### 插入语句

```SQL
INSERT INTO 表名(字段1，字段2,..) VALUES(值1，值2，...);-- 经典写法
/*插入的类型要与字段类型一致，字段与值要一一对应*/
INSERT INTO 表名 SET 字段1=值1，字段2=值2,..;-- set 写法

-- eg
insert into user(id,name,picture) values(3,'test',null);
insert into user values(4,'test',null);-- 插入全部字段 值顺序与表字段顺序一致
insert into user set id=5,name='test';
```

```SQL
-- 批量插入，set写法不支持
INSERT INTO 表名(字段1,..) VALUES 
(值1,..),
(值1,..),..
-- 批量插入子查询结果，set写法不支持
INSERT INTO 表名1(字段1,..) SELECT 值1,... FROM 表2

-- eg
insert into user(name)
select 'a' UNION select 'b' UNION select 'c';-- 向查询结果联合成一个表，再批量插入
```



#### 修改语句

```SQL
-- 修改单表记录：执行顺序 找到表 -> where筛选 -> set更新
UPDATE 表名 SET 字段1=值1，字段2=值2，... [WHERE 条件]
-- eg
update user set name='test',age=10 where id=1
```

```SQL
-- 修改多表/级联修改：执行顺序 关联表 -> where筛选 -> set更新
UPDATE 表1 [连接类型] JOIN 表2 ON 条件 SET 表1.字段1=值1，表2.字段1=值2，... [WHERE 条件]
-- eg
update user u inner join role r on u.role_id=r.id 
set u.name='test',r.name='admin'
where u.age > 20
```



#### 删除语句

```SQL
-- 方式一
DELETE FROM 表名 [WHERE 条件];-- 单表删除，不加条件会清空表中所有记录
DELETE 表1，表2 FROM 表1 [连接类型] JOIN 表2 ON 连接条件 [WHERE] 条件；-- 多表级联删除
-- 方式二，truncate 删除不能加条件，用来清空表，效率高于delete
TRUNCATE TABLE 表名;-- 删除整表数据

-- delete与truncate的区别
1. delete支持where条件，truncate不支持
2. truncate清空表，效率高于delete
3. delete清空表表后，插入数据的自增字段从断点开始，而truncate清空后从1开始
4. delete删除会返回删除成功的行，truncate没有返回值
5. delete删除可以回滚，truncate删除不能回滚
-- eg
 delete from user where id = 1;
 delete u,r from user u inner join role r on u.role_id = r.id
 where u.id = 1;-- 删除 id=1的user所对应的role记录和user记录
```



### DDL

数据定义语言  库和表的管理：创建修改删除

#### 库管理

关键字：create，alter，drop

```SQL
-- 创建库
CREATE DATADASE [IF NOT EXISTS] 库名；-- 创建库，使用if not exists 提高容错性
-- 修改库，一般不使用，可能导致数据丢失
ALTER DATABASE 库名 CHARACTER SET UTF8;-- 修改字符集
-- 删除库
DROP DATABASE [IF EXISTS] 库名;-- 删除库

-- 常用写法
DROP DATABASE IF EXISTS 库名;CREATE DATABASE 库名；
```



#### 表管理

关键字：create , alter , drop

```SQL
-- 创建表
CREATE TABLE [IF NOT EXISTS] 表名( 
    字段名 类型[(长度)] [约束],
    字段名 类型[(长度)] [约束],
    ...
);
-- 表的复制，跨库操作需要在使用 库名.表名
CREATE TABLE 表1 LIKE 表2；-- 复制表2的结构创建表1
CREATE TABLE 表1 SELECT * FROM 表2；-- 使用表2的结构和数据创建表1
CREATE TABLE 表1 SELECT 列1,列2 FROM 表2 WHERE 1=2;-- 只复制指定的结构，不要数据,或 WHERE 0
-- 删除表
DROP TABLE [IF EXISTS] 表名;

-- 常用写法
DROP TABLE IF EXISTS 表名；CREATE TABLE 表名();
```

```SQL
-- 修改表：
ALTER TABLE 表名 CHANGE|MODIFY|ADD|DROP COLUMN 列名 [类型] [约束]

-- 修改列名
ALTER TABLE 表名 CHANGE [COLUMN] 列名 新列名 类型;
-- 修改列类型/约束
ALTER TABLE 表名 MODIFY COLUMN 列名 [类型] [约束];
-- 新增列
ALTER TABLE 表名 ADD COLUMN 列名 类型 [约束];
-- 删除列
ALTER TABLE DROP COLUMN 列名;
-- 修改表名
ALTER TABLE 表名 RENAME TO 新表名;

-- eg
alter table user change column createTime cTime datetime;
alter table user modify column cTime timestamp;
```



#### 数据类型

##### 数值型

默认有符号，可以使用 int unsigned 来设置无符号

使用原则：越简单越好，越小越好

整数

```SQL
-- 整型
	tinyint 1字节；有符号 -128~127 ,无符号 0~255
	smallint 2字节；mediumint 3字节;
	int/integer 4字节；bigint 8字节
	-- 默认有符号,设置无符号
ALTER TABLE book MODIFY COLUMN id INT UNSIGNED;-- 设置无符号
ALTER TABLE book MODIFY COLUMN id INT ZEROFiLL;-- 设置使用0填充，默认为无符号
    /*
    数值超出范围 报 out of range ，并插入临界值
    默认设置了长度，实际长度由类型决定，手动指定的长度表示显示时的宽度
    */
```

小数

```SQL
-- 小数
	-- 浮点型
	float(M,D) 4字节，double(M,D) 8字节
	-- 定点型
	dec(M,D)/decimal(M,D) M+2字节，最大范围与double相同，精度更高
	/*
	M:整数位数+小数位数；D:小数位数；
	不写(M,D) : float与double根据数据确定精度，decimal默认(10,0)
	*/
```

##### 字符型

```SQL
-- 短文本
M:最多 字符 数
char(M) --固定长度，M个字符，M默认为1，空间浪费，但性能稍强与varchar
varchar(M) --可变长度，最多M个字符
binary和varbinary --存储二进制文件
enum('male','female') --枚举类型，插入值只能在规定的范围内，不分大小写
set('a','b','c') --集合类型，类似enum但是可以插入多个值,不分大小写，如values('a,b,C')
-- 长文本
```



##### 日期型

```SQL
date 4字节，time 3字节，year 1字节 --保存日期，时间，年
-- 日期+时间
timestamp 4字节
datetime 8字节
/*
 timestamp表示的时间日期范围小于datatime
 timestamp更能表示实际日期和时间
*/
```



#### 约束（constraint)

限制表中数据，保证数据一致性

```SQL
-- 创建表时添加约束
CREATE TABLE [IF NOT EXISTS] 表名( 列名 类型[(长度)] 列级约束1 [列级约束2],... , 表级约束)
-- 修改约束
	-- 添加约束
	ALTER TABLE 表名 MODIFY COLUMN 列名 类型[(长度)] 列级约束; -- 列级写法，不支持取名
	ALTER TABLE 表名 ADD [CONSTRAINT 约束名] 表级约束(列名); -- 表级写法
	-- 删除约束
	ALTER TABLE 表名 MODIFY COLUMN 列名 类型[(长度)]; -- 列级写法
	ALTER TABLE 表名 DROP PRIMARY KEY; -- 表级删除主键
	show index from 表名；-- 查看键名
	ALTER TABLE 表名 DROP INDEX 唯一键约束名;  -- 表级删除unique约束
	ALTER TABLE 表名 DROP FOREIGN KEY 外键名；-- 表级删除外键
```

```SQL
-- 六大约束：主键，外键，唯一键自动生成索引
NOT NULL -- 非空约束，保证该字段值不能为空
DEFAULT 值  -- 默认值约束，该字段有默认值
PRIMARY KEY -- 主键约束，保证该字段唯一且非空，一张表只能有一个主键，支持联合主键
UNIQUE   -- 唯一约束，只能有一个空，支持联合unique(列1，列2)
-- 外键约束，限制两表关系，在从表添加该约束，用于引用主表字段的值
FOREIGN KEY(列名) REFERENCES 主表(键) 
CHECK(gender IN('男','女'))   -- 检查约束，MySQL不支持，使用无效果

-- 列级约束：写在字段类型后
 FOREIGN KEY 不能做列级约束，无效果
-- 表级约束：写在最后一个字段之后 逗号分隔
 NOT NULL 和 DEFAULT 不可以做表级约束，无效果
 [CONSTRAINT 约束名] 约束类型(字段)
 
 -- 通用写法：只把外键写在表级约束，格式如下
 constraint fk_当前表_主表 foreign key(外键) references 主表(键) 
 [ON DELETE RESTRCT ON UPDATE CASCADE] -- 删除主表记录时如果有关联则不允许删除，更新主表时有关联记录则级联更新
```



```SQL
-- eg 行级约束
create table if not exists user(
    id INT PRIMARY KEY,
    name VARCHAR(20) NOT NULL,
    gender VARCHAR(2) CHECK(gender IN ('男','女'))，-- MySQL使用无效
    age INT DEFAULT 20,
    code INT UNIQUE
)
-- eg 列级约束
create table if not exists user(
	id int,code int,role_id int,
    name varchar(20) not null,
    age int default 20,
    
    [contains pk] primary key(id),
    [contains code] unique(code),
    [contains fk] foreign key(role_id) references role(id) 
)
```

##### unique与primary的区别

* 都保证数据一致性，
* unique允许有一个空值，primary key不允许为空，
* 一张表可以有多个unique约束，但是只能有一个primary key
* unique(列1，列2)支持联合，primary key(列1，列2)支持联合主键

##### 外键

constraint fk_从表\_主表  foreign key(列) references 主表(键)

* 要求在从表中设置外键

* 两关联键的类型要一致
* 主表中的关联字段必须是键（primary key  , unique ）
* 插入时先操作主表；删除数据时先操作从表



#### 标识列/自增长序列

```SQL
--
auto_increment -- 要修改起始位置就手动插入值
-- 创建表时设置自增
create table if not exists user(
    id int primary key AUTO_INCREMENT;
)
-- 修改表时设置
alter table user modify column id primary key auto_increment;
alter table user modify column id primary key;-- 删除自增

-- 注意
标识列只能有一个，而且是key （主键，唯一，外键）
标识列类型只能是数值型
通过 set auto_increment_increment=1 来设置步长
```



### TCL

Transaction Control Language ：事务控制语言

#### 支持事务的存储引擎/表类型

数据存储技术，使用 ```show engines```查看支持的存储引擎

常用：InnoDB（支持事务），MyISAM（不支持事务） ，MEMORY(不支持事务)

* 事务：一组SQL语句组成的执行单元，要么都执行，要么都不执行；如果有一条SQL执行错误可以回滚到事务未执行之前的状态
* 事务特性：ACID
  * A（Atomicity）：原子性，事务是不可分割的执行单位
  * C（Consistency）：一致性，事务必须使数据从一个一致性状态转到另一个一致性状态
  * I （Isolation）：隔离性，一个事务的执行不能被其他事务干扰，使用隔离级别来控制
  * D（Durability）：持久性，事务提交后就是对数据的永久性改变

#### 事务的创建

* 隐式事务

  show variables like '%autocommit%'    --> ON ,默认开启自动提交

  列如：insert，update，delete

* 显式事务

  ```SQL
  -- 先关闭自动提交
  set autocommit = 0;-- 1. 关闭自动提交，只针对当前事务有效
  [START TRANSACTION;]  --可选，声明事务开始
  语句1;语句2；语句3；-- 2.语句只能是 select insert update delete 语句
  commit|rollback; -- 3.结束事务：要么提交 commit ,要么回滚 rollback
  
  ---------
  -- eg
  set autocommit=0;
  start TRANSACTION;
  update account set money=money+20 where id = 1;
  update account set money=money-20 where id = 2;
  rollback;
  ```



#### 事务并发问题

多个事务同时访问同一数据

* **脏读**：读取了另一个事务还没提交的临时数据

* **不可重复读**：**针对更新** ，同一事务多次读取的数据不一样

  读取了另一个事务还没提交的临时数据，

  第二次读取时另一事务已经提交 **更新**  ，两次读到的数据不一致

* **幻读**：**针对插入，删除**

  读取数据时，另一事务正在插入/删除记录，再次读取时读到的记录数与上次不一样

##### 通过隔离级别来避免并问题

```SQL
select @@tx_isolation; -- 查看当前隔离级别
set session transaction isolation level repeatable read; -- 设置当前隔离级别
set global session transaction isolation level repeatable read; -- 设置全局隔离级别
```

* Oracle支持2中隔离级别：read commited(默认，避免脏读)，serializable
* MySQL支持4中隔离级别
  * read uncommited：不能避免任何并发问题
  * read commited：避免脏读
  * repeatable read 默认，避免脏读，不可重复读
  * serializable 避免任何并发问题，效率最低



#### 回滚点：savepoint搭配rollback使用

```SQL
set autocommit=0;
start transaction;
delete from user where id =1;
SAVEPOINT a;-- 设置保存点a
delete from user where id =2;
ROLLBACK TO a; -- 回滚到保存点a
```

#### delete 与 truncate在事务中的区别：truncate不支持回滚



### 视图

虚拟表：和普通表格一样使用，通过普通表动态生成

将复杂查询的SQL逻辑封装为视图

特点：临时性，实现SQL语句的重用，简化复杂SQL操作，保护数据(只提部分数据)

并不保存真实数据，只是保存了SQL逻辑

```SQL
-- 创建视图
CREATE VIEW 视图名 AS 复杂SQL语句；
-- 使用视图
SELECT 查询列表 FROM 视图名 [WHERE 条件]

-- eg
create view v1 
	as 
select U.* from user u inner join role r on u.role_id = r.id;
select * from v1 where id=1;
```

```SQL
-- 修改视图
CREATE OR REPLACE VIEW 视图名 AS 复杂SQL语句；-- 存在就修改，不存在就创建
ALTER VIEW 视图名 AS 新SQL语句;
-- 删除视图
DROP VIEW 视图1，视图2，...;
-- 查看视图
DESC 视图名; -- 显示视图结构
show create VIEW 视图名; -- 显示创建语句

-- 视图更新，很少使用,默认会影响到原始表
INSERT INTO 视图(...) VALUES(..);
UPDATE 视图 SET ... [WHERE 条件];
DELETE FROM 视图 [WHERE 条件];
-- 包含以下内容的视图不允许更新
	分组函数，distinct,group by,having,union；
	常量值
	有子查询
	join
	from 视图
```



#### 视图与表的区别

| 类型 | 创建语句                     | 保存类型      | 增删改查       |
| ---- | ---------------------------- | ------------- | -------------- |
| 表   | create table [if not exists] | 保存了数据    | 支持           |
| 视图 | create [or replace] view     | 仅保存SQL逻辑 | 一般不能增删改 |



###  变量

分类：系统变量（全局变量，会话变量），自定义变量（局部变量，用户变量）

```shell
# 系统变量
# 全局变量针对所有会话(连接)有效，重启服务器失效
# 会话变量只对当前连接有效
show global|[session] variables [like '%auto%]';#查看全局|会话变量
select @@global|[session].系统变量名;# 查看指定系统变量
set global|[session] autocommit=0;#为系统变量赋值
set @@global|[session].autocomit=0#为系统变量赋值
```



```shell
#自定义变量
#用户变量针对于当前会话/连接有效
set @变量名=值;#声明并初始化，赋值
set @变量:=值;#声明并初始化，赋值
select @变量:=值;#声明并初始化，赋值
select 字段 into @变量名 from 表；#赋值

select @变量名;#查看
-- eg
set @i=1;set @j=2;set @sum=@i+@j;
select @sum;


# 局部变量，仅仅在定义变量的 begin end中有效，而且要放在第一句
-----------------------------------------------------
declare 变量 类型；#声明变量
declare 变量 类型 default 值；#声明变量并赋值 ！！！
set 变量=值；#赋值 或:=   
select 字段 into 变量 from 表；#赋值   ！！！十分常用
select 变量；#查看
-----------------------------------------------------
```





### 存储过程、函数、触发器

类似于Java中的方法，提高代码重用性，简化操作

需要返回值时使用函数，不需要返回值使用存储过程

* 存储过程

  预先编译好的SQL语句集合，提高代码重用性，简化操作，减少编译次数和连接次数

#### 存储过程 procedure

##### 创建存储过程

要在命名行界面执行

```SQL
-- 创建
DELIMITER $ -- 设置结束标志  ,设置后整个会话的SQL语句都有以$结尾
CREATE PROCEDURE 名称(形参列表)
BEGIN
	一组SQL;
END $ -- 结尾时要交结束符号
-- 使用
CALL 名称()$ -- 也要加结束符号
```

* 说明

  * 参数列表包含3部分

    ```shell
    参数模式         参数名  参数类型 
    in/out/inout	name  varchar(20)
    ```

    ```shell
    参数模式3个
    IN 输入参数，需要传值
    OUT 输出参数，作为返回值
    INOUT 既可以作为输入也可以输出，既需要传值，也要返回
    ```

  * 存储过程只有一句话是 begin end可以省略

  * 每个SQL语句要以分号结尾

  * 存储过程的结尾使用 delimiter 结束标记

* **无参存储过程**

  ```SQL
  DELIMITER $
  CREATE PROCEDURE add_accounts()
  BEGIN
  	INSERT INTO account(name,money) 
  	VALUES
  	('A',90),('B',30),('C',10);
  END $
  CALL add_accounts()$
  ```

* **有参存储过程**

  ```SQL
  DELIMITER $
  CREATE PROCEDURE search_role(IN Sname varchar(20))
  BEGIN
  	SELECT *
  	FROM account a INNER JOIN role r ON a.id = r.id
  	WHERE a.name = Sname;
  END $
  CALL search_role('Abi')$
  ```

* **存储过程+变量使用**

  ```SQL
  delimiter $
  create procedure checkAcc(in name varchar(20))
  begin
  	declare result int default 0;-- 声明变量并初始化
  	-- set @result=0;--或使用用户变量
  	select count(*) into result -- 将查询结果赋值给变量 select into
  	from account a
  	where a.name = name;
  	select IF(result != 0,'存在用户','该用吧不存在') info;-- 打印结果
  end $
  call checkAcc('Abi')$
  ```

* **带输出值的存储过程**

  ```SQL
  delimiter $
  create procedure acc_role(in name varchar(20),out rName varchar(20),out rId int)
  begin
  	select r.name,r.id into rName,rId
  	from account a inner join role r on a.id = r.id
  	where a.name = name;
  end $
  
  call acc_role('Abi',@rname,@rid)$ --调用过程，将返回值存入用户变量
  select @rname,@rid$ -- 查询用户变量
  ```

* **INOUT存储过程**

  ```SQL
  delimiter $
  create procedure multi2(inout a int,inout b int)
  begin
  	set a = a*2;
  	set b = b*2;
  end $
  -- 调用：参数必须有值
  set @x=2$
  set @y=3$
  call multi2(@x,@y)$
  select @x,@y$
  ```

##### 查看和删除存储过程

```SQL
SHOW PROCEDURE 名称;-- 显示创建语句
DROP PROCEDURE 名称; --一次只能删一个
```



#### 函数 function

与存储过程的区别：返回值

```SQL
存储过程可以有0-n个返回值，而函数有且仅有一个返回值；
存储过程一般用于增删改，而函数用来处理数据
```

##### 创建函数

```SQL
-- 创建
DELIMITER $
CREATE FUNCTION 函数名(形参列表) RETURNS 返回类型
BEGIN
	declare 变量 返回类型 [default 值];
	-- set @变量=0; -- 也可以使用用户变量
	函数体;-- 为变量赋值
	return 变量;
END $
-- 调用
SELECT 函数名(实参列表)$
```

* 说明

  * 参数列表包含两部分 ：参数名 类型      ( 参数模式都IN不写)
  * 函数体必须有return语句，一般放在函数体最后
  * 当函数体只有一句话可以省略 begin end
  * 使用delimiter 结束符 结束

* **无参函数**

  ```SQL
  delimiter $
  create function ct() returns int
  begin
  	declare result in default 0;
  	-- set @result=0;-- 也可以使用用户变量
  	select count(*) into result from account;
  	return result;
  end $
  select ct()$
  ```

* **有参函数**

  ```SQL
  delimiter $
  create function money(name varchar(10)) returns double
  begin
  	declare mon double default 0;
  	-- set @mon=0;-- 也可以使用用户变量
  	select a.money into mon
  	from account a
  	where a.name = name;
  	return mon;
  end $
  select money('Abi')$
  ```

##### 查看和删除

```SQL
show create function 函数名;
drop function 函数名;
```



#### 存储过程和函数的存储位置

```SQL
mysql.proc
```



#### 触发器 trigger

触发器是与表有关的数据库对象，在insert/update/delete之后触发SQL集合，可以确保数据库的完整性，进行日志记录，数据校验等操作。

MYSQL目前触发器支持行级触发(Oracle还支持SQL级触发)，使用OLD和NEW变量来表示正在操作的数据

| 触发器类型 | NEW 和 OLD 的作用                      |
| ---------- | -------------------------------------- |
| INSERT     | NEW 表示将要插入或已经插入的数据       |
| UPDATE     | OLD表示修改之前的数据，NEW表示修改之后 |
| DELETE     | OLD表示将要删除或已经删除的数据        |

##### 触发器的创建

```sql
DELIMITER $ 
create trigger 触发器名
before|after insert|update|delete on 表名
[for each row] -- 显示声明行级触发
begin
	一组sql;
end $
```

##### 触发器创建示例

```sql
-- insert 触发 使用new 记录users表插入日志
DELIMITER $
CREATE TRIGGER user_insert
AFTER INSERT ON users
FOR EACH ROW
BEGIN
	insert into user_log(op_type,op_id,op_params)
		values ('insert',new.id,concat(NEW.codes,':',NEW.role_id));
END $
-- update 触发 使用 old 和 new
delimiter $
CREATE TRIGGER user_update
AFTER UPDATE ON users
FOR EACH ROW
BEGIN
	insert into user_log(op_type,op_id,op_params)
		values ('update',NEW.id,concat(concat(old.codes,':',old.role_id),' to ',concat(new.codes,':',new.role_id)));
END $
-- delete 触发 使用old
delimiter $
CREATE TRIGGER user_delete
AFTER DELETE ON users
FOR EACH ROW
BEGIN
	insert into user_log(op_type,op_id,op_params)
		values ('delete',NEW.id,concat(concat(old.codes,':',old.role_id)));
END $
```

##### 查看和删除触发器

```sql
 show triggers;
 drop trigger trigger1[,trigger2,..]
```



### 流程控制

* 分类

  ```SQL
  顺序结构：从上往下一次执行
  分支结构：满足条件时，有选择的执行
  循环结构：满足条件时重复执行
  ```

#### 分支结构

##### IF函数

```SQL
select if(条件语句，表达式1，表达式2);-- 满足条件返回表达式1，不满足返回2
-- 类似于 三元表达式 xxx?:true:false
```

##### CASE函数<a name=">>"></a>

* 特点

  1. 既可以作为表达式嵌套到其他语句中使用（返回值），也可以作为单独的语句（返回表达式）放在begin end中
  2. 匹配到when后，就执行then，然后跳出case，匹配不到执行else，没有else返回null

* 使用

  1. 作为表达式嵌套在其他语句，返回值

     ```SQL
     -- switch用法,case 表达式/值
     select case id
     when 1 then 1 when 2 then 2 else 3
     end --注意 end
     from user;
     -- 多重if用法，case 不跟表达式/值
     select case
     when id <3 then 0 when id>=3 and id<=5 then 1 else -1
     end
     from user;
     ```

  2. 作为语句放在begin end中使用，返回表达式

     ```SQL
     --switch
     delimiter $
     create procedure user_role(in name varchar(20),out r varchar(20))
     begin
     	select a.role_id into r
     	from account a where a.name = name;
     	case r
     		when 1 then set r='admin';
     		when 2 then set r='user';
     		else set r='unknown';
     	end case;
     end $
     -- 多重if
     delimiler $
     create function rich_level(money double) returns varchar(20)
     begin 
     	set @rl = '';
     	case
     		when money <= 10 then set @rl='TuBie';
     		when money between 50 and 100 then set @rl='middle';
     		when money >100 then set @rl='richer';
     		else set @rl='??';
     	end case;
     	return @rl;
     end
     ```



##### IF 语句  注意不是函数

* ____

  ```SQL
  --只能用在begin end中
  IF 条件1 THEN 语句1；
  ELSEIF 条件2 THEN 语句2;
  ...
  [ELSE 语句n]
  END IF; -- 注意
  
  
  -- eg
  delimiter $
  create function score(sc double) returns char(1)
  begin
  	declare lev char(1) default '';
  	if sc <60 then set lev = 'D';
  	elseif sc between 60 and 100 then set lev 'A';
  	else set lev = 'S';
  	end if;
  	return lev;
  end $
  ```

  

#### 循环结构

循环 ：while ， loop ，repeat

控制：iterate（类似 continue），leave （类似 break）

##### while

* ```SQL
  [标签:] while 循环条件 
  do 										-- 注意do，使用控制语句时要加上标签
  	[if 条件 then iterate 标签;end if;] -- 使用标签重新循环
  	[if 条件 then leave 标签;end if;]-- 使用标签跳出循环
  	循环体;
  end while [标签];
  ```

* eg

  ```SQL
  -- 批量插入，没有循环控制
  delimiter $
  create procedure addAcc(in num int)
  begin
  	declare i int default 1;
  	while i<=num 
  	do
  		insert into account(name) values(concat('test',i));
  		set i = i + 1;
  	end while;
  end $
  
  -- 使用 leave 标签  跳出循环
  delimiter $
  create procedure addAcc(in num int)
  begin
  	declare i int default 1;
  	tag:while i<=num -- 设置标签
  	do 
  		insert into account(name) values(concat('test',i));
  		if i>=5 then leave tag; -- 跳出标签
  		end if;
  		set i = i +1;
  	end while tag;-- 结束循环
  end $
  -- 使用iterate 标签 重新循环
  delimiter $
  create procedure addAcc(in num int)
  begin
  	declare i int default 0;
  	tag:while i<num 
  	do
  		set i = i +1;
  		if i%2 != 0 then iterate tag; end if; -- 重新循环
  		insert into account(name) values(concat('test',i));
  	end while tag;
  end $
  ```

  



##### loop，可用来模拟死循环，最好搭配控制语句使用

* ```SQL
  [标签:] loop
  	循环体
  end loop [标签];
  ```

  

##### repeat ，稍微类似do while  只不过条件时结束条件

* ```SQL
  [标签:] repeat
  	循环体;
  until 结束条件;
  end repeat [标签];
  ```



##### while,loop,repeat对比

|        | 语法                                               | 特点         |
| ------ | -------------------------------------------------- | ------------ |
| while  | tag:while 条件 do 循环体; end while tag;           | 先判断后执行 |
| loop   | tag:loop 循环体; end loop tag;                     | 死循环       |
| repeat | tag:repeat 循环体; until 结束条件; end repear tag; | 先执行再判断 |



##### 示例

向表中插入随机字符串

```SQL
create table if not exists content (
	id int primary key auto_increment,
	content varchar(20)
);
delimiter $
create procedure addContent(in num int)
begin
	declare i int default 1;
	declare str varchar(26) default 'abcd....z';
	declare startIndex int default 1;
	declare len int default 1;
	tag:while i <= num 
	do
		set i = i + 1;
		set startIndex = floor(rand()*26+1);
		set len = floor(rand()*(20-statrIndex +1) +1);
		insert into conent(content) values(substr(str,startIndex,len));
	end while tag;
end $
```











 



