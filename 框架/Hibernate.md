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
       <session-factory>配置数据库
           <!--5个必须配置数据库参数-->
           <property name="hibernate.connection.driver_class">
               驱动路径</property>
           <property name="hibernate.connection.url">连接url</property>
           <property name="hibernate.connection.username">jissi</property>
           <property name="hibernate.connection.password">jissi</property>
           <property name="hibernate.dialect"><!--方言-->
               org.hibernate.dialect.MySQLDialect</property>
           <!--映射-->
           <mapping resource="映射配置文件路径"/>
           ...
       </session-factory>
   </hibernate-configuration>
   ```
   
   ```xml
   可选配置参数
   <hibernate-configuration>
       <session-factory>
           上面的必须配置
           <property name="hibernate.show_sql">true</property>终端显示sql
           hibernate.format_sql true  sql语句格式化
           hibernate.hbm2ddl.auto	create 自动创建表
           					  create-drop	创建后删除
           					  update  	没有时创建表，有就正常操作，
           								可用于增加column,开发使用
           					  validate   检查类与表映射是否一致
       </session-factory>
   </hibernate-configuration>
   ```
   
   
   
7. 编写测试类

   ```java
   //创建配置对象
   Configuration cfg = new Configuration();
   cfg.configure();//配置
   //创建SessionFactory对象
   SessionFactory factory = cfg.buildSessionFactory();
   //创建Session对象
   Session se = factory.openSession();
   //创建事务对象
   Transaction tr = se.beginTransaction();
   //编写操作JavaBean对象的代码
   session.save(obj);//保存操作对象
   tr.commit();//tr.rollback()提交事务
   session.close();//关闭会话
   factory.close();//释放资源
   ```

### 二、Hibernate 主要API（有xmind）

1. Configuration配置类

   实例方法：

   ```java
   Configuration config = new Configuration();
   config.configuration();//加载Hibernate配置文件
   SessionFactory factory = config.buildSessionFactory();//创建工厂
   ```

   

2. SessionFactory接口（二级缓存）

   负责初始化Hibernate，创建Session对象，充当数据库代理；

   重量级，**一个数据库用一个SessionFactory对象**；

   常驻内存；线程安全（可以被多个实例共享）

   ```java
   Session session = factoty.openSession();//创建Session对象
   factory.close();//关闭工厂，释放资源；！！！！不要随意使用
   ```

   SessionFactory结构：

   * 内部结构：缓存Hibernate配置以及预制sql语句
   * 外部结构：二级缓存

   关于二级缓存：

   * SessionFactory可以创建多个Session对象
   * Session对象用完就要销毁，生命周期短
   * 每个Session对象都是一级缓存，Session的查询结果可以保存到二级缓存，供其他新的Session对象访问

   根据SessionFactory的特性，使用工具类来简化SessionFactory

   ```java
   public class SessionFactoryUtil{
       private static final Configuration CONFIG;
       private static final SessionFactory FACTORY;
       static{//静态代码块
           CONFIG = new Configration().configure();
           FACTORY = CONFIG.buildSessionFactory();
       }
       public static Session getFactory(){return FACTORY.openSession();}
   }
   ```

3. Session接口（一级缓存）

   负责执行被持久化的CRUD(增删改查)操作

   由SessionFactory产生，非线程安全

   **一次请求使用一个Session，用完要销毁**

   一个JavaBean对象对应一条记录

   注意事项：***先查询再修改，不要手动设置主键***

   实例方法：

   ```java
   参与映射的bean obj;
   session.save(obj);	//保存，需要提交事务
   get(obj.class,主键值);//根据主键访问一条记录，返回该bean对象
   delete(obj);//删除记录，需要先get()出来该obj，需要提交事务
   update(obj);//修改记录，需要先get()出来该obj，需要提交事务
   saveOrUpdate(obj);//保存或添加该obj,先new或get()出该obj，需要提交事务
   Transaction tr = session.beginTransaction();//创建事务
   session.close();//关闭session，释放资源
   ```

   ***查询多个记录***

   ```java
   Query query = session.createQuery("from javaBean");//select *
   List<JavaBean> list = query.list();
   ```

4. Transaction接口

   管理事务

   实例方法：

   ```java
   tr.commit();
   tr.rollback();
   tr.close();//关闭事务，释放资源
   ```

   使用事务的正规流程：

   ```java
   try{ 获取session，开启事务，操作，提交; }
   catch(Exception e){ 回滚;抛出异常; }
   finally{ 释放session; }
   ```

   

   

   