<h1>Hibernate</h1>

<font size=4>关系对象映射（ORM）框架</font>

---

[TOC]

---

### 关于JDBC、框架、ORM、xml约束

> JDBC是接口的规范，没有实现类
>
> 数据库提供的实现类称为驱动
>
> 框架：软件的半成品（完成了部分功能）
>
> ORM：对象关系映射；
>
> ​			关系指的是关系型数据库
>
> ​			ORM通过映射配置文件（xml），将对象与关系型数据库关联，操作对象即时操作数据库

* xml约束

  xml标签是自定义的，需要约束来规定可以出现的标签

  xml约束文件多以dtd，xsd为后缀

  无网络时不显示标签提示：

  ```eclipse - window - reference - 查找xml.Catalog - 选中 ，add  - keytype URI；key 约束中的网址 ；location 本地dtd文件```

### 一、Hibernate框架使用流程

1. 下载Hibernate并解压

   > jpa包：ORM框架规范
   >
   > project/ect：配置文件

2. 创建数据库表结构

3. 导包

   > mysql-connector  	mysql驱动
   >
   > request目录全部包
   >
   > log4j目录全部日志包

4. 编写javaBean

   ```java
   属性与数据流表结构对应
   属性类型最好使用包装类，默认值为null；（避免int 出现值为 0）
   private Long id;
   setter和getter
   ```

5. 映射配置文件

   一个映射配置文件对应一个JavaBean

   一般与JavaBean同目录，名称为```JavaBean名.hbm.xml```

   ```name是JavaBean属性；column是表字段```

   ```xml
   <约束/>
   <hibernate-mapping>
       <class name="类路径" table="对应的表名">把类和表关联
           <id name="类属性" column="主键">配置主键
               <generator class="native"/>主键增长策略安装本地数据库策略
           </id>
           <property name="类属性" column="字段"/>配置属性与字段的对应
       </class>
   </hibernate-mapping>
   ```

6. 核心配置文件

   核心配置文件只有一个：```src/hibernate.cfg.xml```

   ```xml
   <约束：hibernate-configuration.dtd />
   <hibernate-configuration>
       <session-Factory>配置数据库
           <!--5个必须配置数据库参数-->
           <property name="hibernate.connection.driver">驱动路径</property>
           <property name="hibernate.connection.url">连接url</property>
           <property name="hibernate.connection.username">jissi</property>
           <property name="hibernate.connection.password">jissi</property>
           <property name="hibernate.dialect"><!--方言-->
               org.hibernate.dialect.MySQLDialect</property>
           <!--映射-->
           <mapping resource="映射配置文件路径"/>
           ...
       </session-Factory>
   </hibernate-configuration>
   ```

   