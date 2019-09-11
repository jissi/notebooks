<h2>Hibernate框架</h2>

<font size=4>对jdbc的轻量级封装，将数据库当做对象操作</font>

---

<center>Contents</center>
[TOC]

---

### 一、ORM（Object Relative Mapping）

* hibernate 是一种ORM框架
* ORM —— 关系对象映射
* ORM 关注对象与数据库中列的关系

### 二、Hibernate开发步骤

> 1. 创建持久化类   xxx.java
> 2. 创建对象-关系映射文件   xxx.hbm.xml
> 3. 创建Hibernate配置文件   hibernate.cfg.xml
> 4. 通过Hibernate API 编写访问数据库的代码

### 三、HIbernate 应用的编写

* 在数据库创建表

  ```sql
  create table test( id int not null auto_increment,
                    name varchar(30),
                    price float);
  ```

* **编写持久化类，JavaBean，与表对应**

  ```java
  public class Test{
      public int id;
      其他属性，以及getter和setter
  }
  ```

### <font color="red">四、Hibernate配置文件</font>

* Test.hbm.xml  映射文件——持久化类与表的映射

  <font color="green"><!DOCTYPE>约束：idea -   ctl + shift + n 查找 .dtd文件</font>

  ```xml
  <?xml version="1.0" encoding='UTF-8'?>
  <!DOCTYPE hibernate-mapping PUBLIC
          "-//Hibernate/Hibernate Mapping DTD 3.0//EN"
          "http://www.hibernate.org/dtd/hibernate-mapping-3.0.dtd">
  <hibernate-mapping package="包名">
      <class name="Test" table="test"><!--类名对应表名-->
          <id name="id" column="id"><!--id标签定义主键，类属性-字段-->
              <generator class="native"><!--主键的增长方式使用数据库的方式-->
              </generator>
          </id>
          <property name="name" column="name" /><!--属性与字段的对应关系-->
          <property name="price" /><!--column默认使用类属性名-->
      </class>
  </hibernate-mapping>
  ```

* hibernate.cfg.xml 配置文件——数据库驱动，连接，映射文件的位置配置

  ```xml
  <?xml version='1.0' encoding='utf-8'?>
  <!DOCTYPE hibernate-configuration PUBLIC
      "-//Hibernate/Hibernate Configuration DTD//EN"
      "http://www.hibernate.org/dtd/hibernate-configuration-3.0.dtd">
  <hibernate-configuration>
    <session-factory>
        使用property标签来设置数据库连接，sql
        <property name="hibernate.connection.driver_class">
            com.mysql.jdbc.Driver
        </property>
        <!--其他配置项-->
      <property name="dialect">org.hibernate.dialect.MySQLDialect</property>
      <property name="current_session_context_class">thread</property>
      <property name="show_sql">true</property>
      <property name="hbm2ddl.auto">update</property>
        
        <mapping resource="Test.hbm.xml"/><!--指定映射文件-->
    </session-factory>
  </hibernate-configuration>
  ```

  <font color="black">property name属性值</font>

  <font color="red">!!! 注意写法：<property name="connection.username">jissi</property></font>

  > ```shell
  > connection.driver_class	#数据库驱动
  > connection.utl			#连接url：
  > # jdbc:mysql://localhost:3306/dbforjissi?characterEnconding=UTF-8
  > connection.username		# 用户名
  > connection.password		#密码
  > ```

  **数据库配置的写法**

  表示使用mysql方言：Hibernate根据不同的数据库SQL语言来处理，程序员不再关心数据库类型

  <property name="dialect">org.hibernate.dialect.MySQLDialect</property>

  ```shell
  current_session_context_class	:  thread	#每个线程一个事务
  show_sql	:	true	#控制台显示执行的sql语句
  ```

### 五、持久化类的实现类操作数据

