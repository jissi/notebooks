## MySQL



* ## 安装

  ```
  #查看历史版本并卸载
  mysql -V  
  sudo apt-get autoremove --purge mysql-server-版本号
  sudo apt-get autoremove mysql-server
  sudo apt-get remove mysql-common
  ```

  ```
  #安装mysql
  sudo apt-get install mysql-server mysql-client
  #以root身份登录
  sudo mysql -uroot -p
  ```

  

* ## 启动

  $ sudo service mysql start 启动服务

  $ service mysql stop 关闭服务

  $ mysql -u tester -p

  显示数据库：show databases

  * mysql 用户以及密码存储在：/etc/mysql/debian.cnf

  ---

* ## 用户操作

  1. 创建用户

  ​              ``` create user 'tester'@'localhost' identified by 'tester'```

  ​               ```Or : 使用root登录,use mysql; insert into user (...) vaules(...)```

  2. 对用户授权

     ```
       grant privileges (create,select.......) on database_name.*[ or tabel_name ] to user_name               [identified by 'passwd']
     ```

     

  3. 用户操作

     ```
     drop user account
     rename user 'jissi'@'localhost' to new_name
     ```

  4. 连接数据库

     ```
     mysql -h host_name -p -u user_name 
     ```

     

  ---

* ## 数据库操作

  1. 创建数据库：```create database <name>  [default character set utf-8 collate utf8_general_ci]```
  2. 选择数据库 ：```use <name>;```
  3. 删除数据库：```drop database <name>;```
  4. 

  显示表 ：         ```show tables;```

  新建表：       ``` create table <tabel_name>  ( 属性1 int(10)，属性          ```         

  ​                                                                        ```    2 char(20) ) [character set=utf8] ```

  ```
  alter table table_name convert to character set utf8;
  ```

  修改表属性：

  ```
  alter table table_name drop column column_name ;  #删除属性列
  alter table table_name add  column_name char(10) null ; #添加属性列
  alter table table_name modify column column_name char(5) null;#修改列属性
  ```

  查看表属性:

  ```
desc 表名
  show columns from 表名
  ```
```
  
  
  
  
  
  <font align = 'center'>mysql 数据类型</font>
  
  |          |            |                  |                   |
  | -------- | ---------- | ---------------- | ----------------- |
  | 数据类型 | 大小(字节) | 用途             | 格式              |
  | int      | 4          | 整数             |                   |
  | float    | 4          | 单精度浮点数     |                   |
  | double   | 8          | 双精度浮点数     |                   |
  | enum     | --         | 单选,比如性别    | ENUM('a','b','c') |
  | set      | --         | 多选             | SET('1','2','3')  |
  | date     | 3          | 日期             | YYYY-MM-DD        |
  | time     | 3          | 时间点或持续时间 | HH:MM:SS          |
  | year     | 1          | 年份值           | YYYY              |
  | char     | 0~255      | 定长字符串       |                   |
  | varchar  | 0~255      | 变长字符串       |                   |
  | text     | 0~65535    | 长文本数据       |                   |

---

* ## 表操作

  增删改查

  insert into table_name (column_1,column_2) values (value_1,vaule_2)

  delete from table_name where ...

  update  table_name set ... where ...

  select [distinct | unique] column_1  [as alias] ,column_2  from table_name

  ​           [ where column_name = ? ]

  查询

  * select   \[options]   \[fuction] columns from table_name

    <center><font size=3>description of   options</font></center>

  | 操作     | 说明           |
  | -------- | -------------- |
  | distinct | 重复值只取一次 |
  | unique   |                |
  |          |                |

  | 聚合函数          | 作用    |
  | ----------------- | ------- |
  | count( column)    | 统计    |
  | sum(column)       | 求和    |
  | avg( )            | average |
  | max( ) \| min ( ) |         |

  

  1. where  column_name  = | > |< | !=     ?

     ​                                    in (10,20,30)

     ​                                     between 20 and 30

     ​                                     like ‘str* | _’        * for all ;  _ for one

     ​                                     is NUll 

  2. oder by column_name [ columns ][asc [desc] ]

  3. group by column_name

  ---

  

* ## 系统学习sql（from 《SQL 必知必会》）

  * ### 第一课——SQL基础

    1. 注释

```
     --注释
       # 注释
       /* 多行
          注释*/
       ```


​       

    2. 通配符
    
       %	匹配任意多个任意字符 | *
    
       _	 匹配单个字符				 | ?
    
       []	字符集 eg: [1,2,3] means   1 or 2 or 3


​       

    3. SQL 测试语句
    
       ```select 	函数   ```
    
       ```
       trim( col) 	#去掉左右两侧的空格
       Ltrim( col)	#去掉左侧空格
       Rtrim( col)
       now()		#显示日期
       ```


​       

       ​	

  * ### 第二课——检索数据

    ```
    select [distinct] [*| col_1,col_2 ...] from table_name; 
    # tips：distinct针对其后所有列，不用每个列指定一次distinct
    ```

    限制行数

    ```
    select top 5 col_names from table_name ;	# SQL Server,Access 微软数据库
    select col_names 	   from table_name fetch first 5 rows only ; # DB2
    select col_names       from table_name where rownum <= 5 ;	# Oracle数据库
    selsct col_names	   from table_name limit 5 ;		# MySQL,MariaDB等
    
    select col_names	   from table_name limit 5 offset 5 ; <==> limit 5,5
    										    起始行          起始行，行数
    ```

    

  * ### 第三课——排序检索

    #### !!!<font color='red'>order by 字句一定要写在最后</font>

    | select  col_names  from  table_name | order by | col_names                                                    | [ASC]                  |
    | :---------------------------------- | -------- | ------------------------------------------------------------ | :--------------------- |
    |                                     |          | 按该列（必须是select的列）排序，多个col时，先按col_1排序，（如果col_1的值不是唯一的）再将排好的顺序按col_2排序. | 默认ASC升序，DESC降序. |

    

  * ### 第四课——过滤数据

    where字句

    ``` select  col_names  from  table_name  where 条件```

    条件操作符:	>,	<,	>=,	<=,	!=,	!>=,	!<=,	<> (<==> !=)

    范围条件：between 3 and 5 

    空值条件：is null

    存在：	exist 、 not exist

    任何：	any	（... where col < any )

  * ### 第五课——高级过滤

    1. 组合where 字句

        where 条件1 <font color='red'>and</font> 条件2 <font color='blue'>or</font> 条件3

    #### ！！！<font color='red'>and优先级高于or，括号可以提升优先级</font>

    2. in 操作符

        ... where col_name <font size=5 color='red'>in</font> (1,2,3)

    3. not 操作符

       否定其后的任何条件
       
       

  * ### 第六课——通配符过滤

    like 操作符

     	... where col_name <font color='red' >like 'xx%'</font>

    通配符：

    %	匹配任意多个任意字符 | *

    _	 匹配单个字符				 | ?

    []	字符集 eg: [1,2,3] means   1 or 2 or 3

    

  * ### 第七课——拼接字段

    字段即列（column）<font color='red'>	concat( )</font>

    ```
    select concat( col_1 , '(' , col_2 , ')' ) from ...	  # for mysql, mariadb
    select col_1 + '(' + col_2 + ')' from ...			  # for MicroSoft 
    select col_1 || '(' || col_2 || ')'  from ...		  # for Oracle ...
    
    使用trim()函数去掉空格
    select trim( col_1 ) + '(' ...
    ```

    指定别名<font color='red'>	as 关键字</font>

    ```
    select col_1 as col from ...
    ```

    算术运算<font color='red'>	+, - ,* , /</font>

    ```
    select col_1 * col_2 as col from ...
    ```

    

  * ### 第八课--用函数处理数据

    文本操作函数

    ```
    trim( col_1 )、ltrim()、rtrim()	#去掉括号
    left(col_1,2),right()			 #返回col_1或str的左边、右边的2个字符
    lower(),upper()					 #转大小写
    length()						 #长度
    soundex()						 #读音，一般用于where子句 soundex(col_1) = 			    														sounded('haah')
    ```

    时间日期函数（各个dbms差别很大）

    ```
    ...where year(col_date)=2012			#mysql
    ...where datepart(yy,col_date)=2012		#sql server
    ```

    数值处理函数

    ```
    abs()					#绝对值
    sin(),cos(),tan()		#三角函数
    exp()					#指数值
    sqrt()					#平方根
    pi()					#圆周率
    ```

    

  * ### 第九课--汇总数据

    聚集函数（计算并返回值）

    ```
    avg()			#求均值（忽略null）
    count()			#返回某列的行数(记录数),有参数时忽略null行，count(*)不忽略null行
    max(),min()		#最大，最小值，忽略null
    sum()			#求和，忽略null
    ```

    <font color='red'>	聚集不同值：distinct（聚集函数默认使用all：count( all col_1）</font>：可用于count( distinct col )	,但不能用于count(*) 

    组合聚集函数：select 子句中包含多个聚集函数

  * ### 第十课--分组数据

    

    ```select ... from ... where ... group by ... having ... order by ...```

    分组：<font color='red'>group by</font>

    ```
    tips:
    group by 可以包含任意数目的列，进行嵌套分组
    嵌套分组时，数据将在最后指定的分组上进行汇总
    每一列必须是检索列或有效表达式 （不能是聚集函数）
    不允许group by 带有可变长数据类型
    除了聚集函数外，select语句中的每一列都必须在group by 中给出
    null为一个分组
    ```

    过滤分组：<font color='red'>having</font>

    ```
    having 与 where 用法，操作符相同
    where在分组前过滤行；having在分组后过滤分组
    ```

    

  * ### 第十一课--使用子查询

    ​     子查询：嵌套在其它查询中的查询

    <font color='red'>使用子查询过滤数据</font>

    ​	<font color='green'>子查询语句中只能检索一列</font>

    ```
    select ... from ... where col_0 in 
    									(select col_1 from ... where col_2 = 
    										(select col_3 = ... from ...)
    									)
    ```

    <font color='red'>作为计算字段使用子查询</font>

    ```
    select col_1 
    		(select count(*) from ... where ...) as col_
    from ...
    ```

    

  * ### 第十二课--联结表

    多表查询 <font color='red'>inner join on：内联结</font>

    ##### 使用where字句联结，或使用inner join 联结都是等值联结即 内联结

    在不是用where xxx=xxx 或  inner join的情况下，默认是笛卡尔积联结

    ```
    select tab1.col1 , tab2.col2 from tab1,tab2
    					where tab1.id = tab2.id
    或者
    select tab1.co1 , tab2.col2 from
    				tab1 inner join tab2 
    					on tab1.id = tab2.id
    ```

    

  * ### 第十三课--高级联结

    表别名

    <font color='green'>表别名不仅可用于select，where还可以用于其它字句</font>

    ``` select ... from tab1 as T1 , tab2 as T2  where T1.id = T2.id order by T1.id```

    ##### <font color='red'>自联结：自己联结自己</font>

    意义：自联结比子查询更快

    ```select T1.id from tab as T1 , tab as T2 where T1.num = T2.num```

    使用子查询

    ```
    select tab.id from tab where tab.num =
    						(select tab.num from tab where ...)
    ```

    ##### <font color='red'>自然联结：相同列只出现一次</font>

    一般的内联结都是自然联结

    ```select T1.* , T2.id ... from tab1 as T1 , tab2 as T2 where T1.id = T2.id```

    ##### <font color='red'>外联结：使用 [　left |right | full ] outer join</font>

    <font color='green'>left , right 是指保留左侧，右侧的表（无关联列）不被修改,而full全外联结保留两个表中无关联的列</font>

    

    ```select tab1.id from tab1 left outer join tab2 on tab1.id=tab2.id```

    

  * ### 第十四课--组合查询

    ##### <font color='red'>Union</font>

    <font color='green'>union 自动去掉重复的行，使用 union all 可不去重；但是where只能去重</font>

    <font color='green'>Union 的order by 只能用在最后一个select语句</font>

    ```
    select 第一个select选择的列 from ... where ... 
    					Union
    select 第一个select选择的列 from ... where ... Order by ...
    ```

    以上Union用法与下列where字句等价

    ```
    select col_1  from tab1 where ... or ...
    ```

    ##### 注意事项

    Union 必须有 >= 2 条select语句

    ​			每个查询必须包含相同列，表达式，或聚集函数

    ​			列数据类型必须兼容

    

  * ### 第十五课--插入数据

    ##### 插入完整的列

    ```
    insert into tab values ('...','...',NULL) 次序要与列次序一致，没有值用NULL填充
    insert into tab (指定列次序) values (对应值)  指定插入某列，由于给出了列名次序，表结构改变 													后仍能使用
    ```

    ##### 插入行的一部分

    ```
    insert into (指定部分列) values (对应值)
    	忽略的列必须具有一下条件：
    		该列允许NULL or 该列有默认值
    ```

    ##### 插入查询结果（可以一次插入多行）

    ```
    insert into tab2 (列名) 
    		select 列名 from tab1 where ...
    ```

    ##### 复制表

    <font color='red'>mysql</font>

    ```screate table tab2 as select * from tab1```

    

  * #### 第十六课--更新和删除数据

    ##### 更新数据：<font color='red'>update ... set ... [where..]</font>

    ```
    update tab set col1 = value1 [col2 = value2,...] where ...
    ```

    <font color='green'>不使用 where子句时   更新该列所有值</font>

    <font color='green'>... set col = NULL  ...  即是删除某个值</font>

    ##### 删除数据：<font color='red'>delete from ... [where  ...]</font>

    ```
    delete from tab where ...
    ```

    <font color='green'>不使用 where 子句时   删除所有行（仅删除数据，表结构仍然存在）</font>

  * #### 第十七课--创建和操纵表

    ```
    create table tab (
    					id int(5) not null,
    					name char(10) ,
    					age int(3) default 20,
    					...,
    					)
    ```

    ##### 指定 not null （默认 null）

    ​	null 与 ‘ ’ 有区别：null表示为空，而 ‘ ’ 表示有值，值为空格

    ##### 指定默认值 default value

    ​	default current_date()		默认当前日期(mysql)

    ##### 更新表 alter table ...

    <font color='green'>alter table ... 	*add  | drop column | modify column*	col ....  </font>

    ```
    增加列		alter table tab add col char(10) not null 
    删除列		alter table tab drop column col
    修改列属性  alter table tab modify column col int(10) null default 10
    修改列名	alter table tab Change col_old col_new varchar(10)
    查看列属性  show full columns from table_name
    ```
  ```
  
  ##### 重命名表：Rename Table  old_name To new_name
  
  ​```rename table tab to newtab```
  ```

* #### 第十八课--视图
  
  ##### 视图并不真实存在，是一种虚拟表
  
  <font color='red'>视图的更新（修改，删除）会影响到原表</font>
  
    ```
    create view newview (col1,col2,...)
    		as 
    	select col1,count(col2), ... from tab
    	where ...
    ```
  
    drop view newview
  
  
  
  * #### 第十九课--



