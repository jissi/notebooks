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

---

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
           <property name="hibernate.dialect"><!--方言 mysql 5.0一下使用MySQLDialect , 5.0 以上使用MySQL5Dialect-->
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

---

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

   负责执行被持久化的CRUD(增查改删)操作

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

---

### 三、持久化类以及持久化对象的状态

> 持久化类：与表建立映射关系的JavaBean；
>
> ​					JavaBean + xxx.hbm.xml

<font color="pink">持久化对象具有自动更新数据库的能力，可以不再写session.update(obj)</font>	[why?](#why)

1. 持久化类的编写规则
   * public 无参数构造方法
   * setter和getter
   * 属性尽量使用基本基本数据类型的包装类
   * **提供一个唯一标识主键属性（唯一标识OID，用以区分缓存中的对象）**

2. 持久化对象的状态

   | 状态   | 说明                                                 |
   | ------ | ---------------------------------------------------- |
   | 瞬时态 | 没有OID，没有被纳入Session管理（对象不在一级缓存中） |
   | 持久态 | 有OID，已经被Session管理                             |
   | 脱管态 | 有OID，没有被Session管理                             |

   > 获得瞬时态：new 持久化对象
   >
   > 获得持久态：session.get(xx.class,主键值)
   >
   > 获得脱管态：手动设置主键

   >瞬态->持久：session.save(obj) / session.saveOrUpdate(obj)
   >
   >瞬态->脱管：不推荐，手动设置主键
   >
   >---
   >
   >持久->瞬时：session.delete(obj)
   >
   >持久->脱管：session.close() / session.clear() 清空缓存
   >
   >---
   >
   >脱管->瞬时：obj.set主键(null)
   >
   >脱管->持久：session.update(obj) / session.saveOrUpdate(obj)

---

### 四、主键生成策略

1. 主键分类

   * 自然主键：对象本身的唯一标识属性作为主键
   * 代理主键：人为增加一个没有其他含义的字段作为主键

2. 主键生成策略

   配置文件：XXX.hbm.xml

   ```<id name="" column=""> <generator class=“native”/> </id>```

   **generator的class取值即是主键生成策略**(常用：native和uuid)

   | 策略         | 适用主键类型             | 生成方式                               |
   | ------------ | ------------------------ | -------------------------------------- |
   | increment    | short，int，long         | 查找最大主键值并+1（并发访问容易出错） |
   | indentity    | short，int，long，mysql  | 要求数据库有自动递增能力（Oracle没有） |
   | sequence     | short，int，long，oracle | mysql用不了                            |
   | ***native*** | ***short,int,long***     | 本地策略，根据数据库类型自动选择策略   |
   | ***uuid***   | ***char，varchar***      | 随机唯一字符                           |
   | assigned     |                          | 自己维护，手动设置                     |

---

### 五、一级缓存——Session

*只要使用Session对象，就有一级缓存*

业务层请求Session访问数据库，session将查到的数据保存到自己的List list中；

SessionFactory作为二级缓存，增强一级缓存Session；默认关闭（没有实现类）

* <a name="why">Session的结构</a>：缓存区+快照区
  * 执行查询（session.get）后，查询结果在缓存区和快照区各保存一份
  * obj.setX(…)修改了缓存区
  * 事务提交时，对比缓存区与快照区，发现数据被修改，更新快照，更新数据库

* 控制Session缓存的实例方法
  * session.clear()：清空缓存
  * session.evict(obj)：删除指定的缓存区对象
  * session.flush()：手动对比缓存区和快照区，并执行相应的操作

---

### 六、事务！！！

* 事务的定义：一组操作，要么都执行，要么都不执行
* <font color="red">事务的特性</font>
  1. 原子性：最小组成单位，不可分割
  2. 一致性：执行前后数据一致
  3. 隔离性：并发访问时，多个事务互不干扰dd
  4. 持久性：一旦提交，永久保存

* <font color="red">不考虑隔离性 带来的后果</font>

  1. 脏读：一个事务读到另一个事务还没提交的数据
  2. 不可重复读：一个事务读到另一个事务提交的update数据，导致同一事务多次查询结果不一致
  3. 虚读：一个事务读到另一个事务提交的insert数据，导致同一事务多次查询结果不一致；

  解决方法：设置数据库隔离级别：

  ```hibernate.cfg.xml```

  ``` <property name=“hibernate.connection.isolation”>4</property>```

  | 隔离级别               | 能够解决的问题        | 在配置文件的编号 |
  | ---------------------- | --------------------- | ---------------- |
  | 未提交读               | 不能                  | 1                |
  | 已提交读（oracle默认） | 避免 脏读             | 2                |
  | 可重复读（mysql默认）  | 避免 脏读和不可重复读 | 4                |
  | 串行化                 | 所有                  | 8                |

* <font color="red">丢失更新：更新的数据丢失</font>

  一个记录同时被修改，先提交修改的数据会被覆盖，导致更新丢失

  解决方案：

  1. 悲观锁：在sql语句后加 for update 将数据上锁，提交后才解锁

  2. 乐观锁：为持久化对象添加```version```属性，先提交的version+1，version小的不能提交

     ```xml
     乐观锁实现：
     对持久化对象：新增 private Integer version; setter,getter方法
     修改配置文件xxx.hbm.xml
     <class name="" table="">
         <id name="" column=""></id>
         <version name="version" />
     </class>
     ```

---

### 七、将Session绑定到线程

* web层：Servlet是单例，第一次访问时创建，每次访问都开启新的线程
* 业务层：传统连接数据库：新线程[ThreadLocal类](#threadlocal)存值
* 数据层：传统方式：通过ThreadLocal取出数据库连接对象，操作数据库
* 业务层可以通过传统方式将session对象存入ThreadLocal对象来传递到数据层

***将session绑定到当前线程，业务层创建session，数据层获取session并操作数据库***

1. 设置hibernate.cfg.xml属性

   ```<property name=“hibernate.current_session_context_class”>Thread</property>```

2. 编写SessionFactory工具类，获取当前线程的session对象（session与线程绑定）

   ```java
   Session session = factory.getCurrentSession();
   ```

3. 数据层获取当前线程中的session对象

   ```java
   Session session = 工具类中获取session的方法; //与
   ```

4. session会随线程一起关闭，不需要再写session.close();

* <a name="threadlocal">关于ThreadLocal类</a>

  java.lang.ThreadLocal类，其底层是Map

  Map<“当前线程”，要存入的值>

  get（）方法返回当前线程key的值

  set（值）取当前线程的值

---

### 八、查询！

* Query接口（HQL语言）

  查询所有：

  ```java
  Query query = session.createQuery("from JavaBean");
  List<JavaBean> list = quert.list(); //list保存了每一个记录对象
  ```

  条件查询：

  ```java
  Query query = session.createQuery("from JavaBean where age > ?");
  query.setInteger(0,18);//？的索引从0开始
  query.list();
  可以使用 :别名 取代？,提高可读性
  query.setInteger("别名",18);
  ```

  模糊查询：

  ```java
  createQuery("from JavaBean where like :name");
  query.setString("name","%小%");
  ```

* Criteria查询接口：完全面向对象的查询，适合条件查询

  Restrictions类是Criteria类的工具类

  ```java
  Criteria criteria = session.createCriteria(JavaBean.class);
  //不添加条件时默认查询所有
  List<JavaBean> list = criteria.list();
  //添加条件
  criteria.add(Restrictions.gt("age",18));//age属性大于18
  criteria.add(Restrictions.like("name","%小%"));//name属性 like %小%
  ```

----

### 九、一对多和多对多表结构的操作

* ##### 一对多：

  一个用户对应多个订单，订单表设置外键指向用户表主键；

  **外键的取值只能从主键取，可以为null**；

  在多方设置外键

  持久化类与配置文件的写法：

  1. 一方——用户表

     * 一方Bean写法：除了设置所有字段属性以及setter和getter外，新增集合：

       ```java
       private Set<多方Bean> set = new HashSet<>();
       //集合用来存放多个 多方对象，务必初始化
       //提供 setter和getter
       ```

     * 一方配置文件新增：

       ```xml
       <set name="集合名" casecade="seve-update,delete" inverse="true">
           <key column="外键名" />
           <one-to-many class="多方类path"/>
       </set>
       ```

  2. 多方——订单表

     * 多方Bean写法

       *除了外键*，其他字段都设属性以及setter和getter，新增：

       ```java
       private 一方Bean 一方对象;//不要初始化
       //提供 setter和getter
       ```

     * 多方配置文件新增：

       ```xml
       <many-to-one name="一方对象" cascade="save-update"
                    column="外键名"
                    class="一方类path" />
       ```

  3. 级联保存/删除 和 放弃外键维护权

     ```不设置级联的情况下，将多方与一方关联，需要一方将多方加入集合&&多方将一方设为属性值，才可以将他们关联（——双向关联）```

     ```外键维护权默认开启，每次操作，一方和多方都会维护主键，产生两次sql```

     * cascade属性：级联，操作一个对象，自动操作其相关的对象

       ```java
       none  不使用级联，默认    
       save-update  级联保存或更新    
       //两方同时使用：多方关联一方，一方关联另一个多方，session.save(多方)
       //用在某方，另一方可以不用执行关联操作：单向关联
       delete  级联删除，慎用    
       //删除时，自动删除与其相关的对象，最好只用在一方上
       delete-orphan  孤儿删除，只存在在一对多环境   
       //解除关系：多方被一方从Set中移除；
       //孤儿删除：从数据库删除
       all  save-update + delete    
       all-delete-orphan  all + delete-orphan
       ```

     * inverse属性：只有一方可以放弃外键维护
     默认情况下：一方使用Set维护外键，多方使用一方对象维护外键

       让一方放弃外键维护可以减少SQL语句
  
       ```
     inverse="true" 放弃外键维护
       inverse="false" 维护外键，默认
       ```
       
       注意：一方放弃外键维护权后，使用一方去关联多方时，多方的外键无人维护，最好用多方去关联一方，不要使用级联删除！！
       
       
     
  
* #### 多对多

  多个用户，每个用户对应多个角色

  > 用户表：uid（主） uname…
  >
  > 角色表：rid（主） rname
  >
  > 中间表：uid rid    （外键组合成的联合主键）

  手动建表需要3个，使用Hibernate只用配置两个多表的JavaBean

  对于用户表：一个用户对应多个角色——使用Set存角色

  对于角色表：一个角色对应多个用户——使用Set存用户

  JavaBean写法：

  ```java
  两个多方除了基本的属性和getter setter外，都新增Set
  private Set<另一个多方类> set = new HashSet<>();
  为Set设置setter和getter
  ```

  配置文件写法：

  ```xml
  双方配置文件类似
  <hibernate-mapping>
      <class name="当前Bean类路径" table="表名">
          <id name="主键属性" column="主键">
              <generator class="native"/>
          </id>
          <property name="属性" column="对应的字段"/>
          ...
          <!--配置中间表-->
          <set name="当前Bean的Set属性" table="中间表名">
              <key column="当前Bean在中间表的外键"/>
              <many-to-many column="另一个Bean在中间表的外键"
                            class="另一个Bean类路径"/>
          </set>
      </class>
  </hibernate-mapping>
  ```

  **注意**：必须有一个多方放弃外键维护（双方都维护联合主键的情况下，会出现主键冲突，报错）

  使用级联：最好在没有放弃外键维护的多方配置级联；**不要使用级联删除，可以通过操作集合来操作中间表，实现删除**

---

### 十、HQL（Hibernate Query Language）查询操作

***面向对象的查询语言，HQL写的都是对象和属性，不是字段！！！***

* 基本查询

  ```java
  Query query = session.createQuery("from JavaBean");//返回Query对象
  List<JavaBean> list = query.list();//返回JavaBean对象列表
  ```

* 别名查询

  HQL不允许 ```select *```

  ```java
  "from bean JavaBean bean"  表示 select * from JavaBean bean;
  ```

* 排序

  ```java
  "from JavaBean order by 属性 asc"
  ```

* 分页查询（起始位置 = （当前页面-1）×查询行数）

  ```java
  SQL写法：limit 0,3
  HQL写法：
  query.setFirstResult(0);//起始位置，基零
  query.setMaxResults(3);//查询3行；
  ```

* 条件查询

  ```java
  "from JavaBean bean where bean.属性 = ?" 或使用 :别名 取代？
  query.set类型(index,值);index基零
  或者 query.set类型("别名"，值);
  或者 query.setParameter(inxex或别名，值);
  ```

* 投影查询指定字段

  ***！！！ query.list()  默认返回 Object[ ]构成的 List***

  ```java
  "select 属性1,属性2 from JavaBean"
   List<Object[]> list = query.list();
  ```

  **在JavaBean中提供使用要查询的字段作为参数的构造方法！！再手动写无参数构造方法**

  ```java
  "select new JavaBean(属性1,属性2) from JavaBean"
   此时：query.list() 返回JavaBean对象构成的List，其他为提供的属性为null
   List<JavaBean> list = query.list();
  ```

* 聚合函数查询，可以使用```*```

  ```java
  count(),sum(),avg(),max(),min()
  "select count(*) from JavaBean"
  List<Number> list = query.list();
  //使用 Number类(基本数值类型的父类)，不用考虑返回的数值类型
  list.get(0).intValue();//取出第一个数值数据，并转为int
  ```

  

---

### 十一、HQL 多表查询

* 关于sql的内连接和外链接

  1. 内连接（inner join  on）：***取两表关联的部分（交集）***

     ```sql
     显式内连接（写连接语句）
     select * from Users u inner join Orders on u.id = o.id;
     隐式内连接：
     select * from Users u , Orders o where u.id = o.id;
     ```

     

  2. 外连接（left join on）：***取左/右表 + 关联部分（交集）***

     ```sql
     左外连接：
     select * from Users u left join Orders o on u.id = o.id;
     右外连接：
     select * from Users u right join Orders o on u.id = o.id;
     ```

* HQL连接查询语句

  ***inner join  和 left join***：没有on ，没有右连接

  1. 默认返回结果  Object[]  数据重复

     ```sql
     "from Users u inner join u.维护外键的属性"   没有 on
     query.list()  返回 Object[] 型List；并且数据有重复
     ```

  2. 转换为JavaBean对象（left/inner join fetch），并去重（转为Set集合）

     ```java
     "from Users u inner join fetch u.orders";fetch ：封装数据到对象
     List<Users> list = query.list();//注意
     Set<Users> set = new HashSet<>(list);
     ```

  

---

### 十二、QBC（Query By Criteria）查询

* QBC查询接口

  > Criteria  总接口的子接口：需要Session对象创建
  >
  > DetachedCriteria  总接口的实现类：离线查询，创建时不需要session对象

* Criteria查询

  Criteria工具类：Restrictions 查询条件、Projections 聚合函数

  1. 基本查询

     ```java
     Criteria criteria = session.createCriteria(JavaBean.class);
     //设置条件
     List<JavaBean> list = criteria.list();
     ```

  2. 条件查询

     ```java
     criteria.add(Restrictions.eq("属性",值));
     criteria.add(Restrictions.in("属性",取值集合或列表对象))；
     ```

  3. 排序

     ```java
     criteria.addOrder(Order.asc("属性"));
     ```

  4. 分页（与HQL类似）

     ```java
     criteria.setFirstResult(0);
     criteria.setMaxResults(3);
     ```

     1. 聚合函数

     ```java
     criteria.setPojection(Projections.count("属性"));
     criteria.setProjection(null);//取消聚合函数
     ```

* DetachedCriteria离线查询

  可以在web层（Servlet）拼接DetachedCriteria查询条件

  ```java
  DetachedCriteria criteria = DetachedCriteriaForClass(JavaBean.class);
  //查询条件
  criteria.addOrder(Order.desc("属性"));
  ```

  将DetachedCriteria对象通过Service层传到Dao层

  ```java
  Criteria executable = criteria.getExecutableCriteria(session);
  List<JavaBean> list = executable.list();
  ```

---

### 十三、SQL语句查询

* ```sql
  SQLQuery query = session.createSQLQuery("sql语句");
  query.list();返回Object[] List
  
  query.addEntity(JavaBean.class);
  query.list();返回JavaBean List
  ```

  

---

### 十四、HIbernate优化：延迟加载

* 默认已经使用延迟加载

  延迟加载：一次发送多少sql（lazy属性），发送sql查询的格式（fetch属性）

* 类级别的延迟加载

  ```xml
  <class name="" table="" 
         lazy="true"  默认，延迟加载
         		"false" 关闭延迟加载
         >
  </class>
  ```

  ```java
  Users user = session.get(Users.class,1L);//不延迟，直接发送一条sql
  
  Users user = session.load(Users.class,1L);//不发送sql
  System.out.print(user.getLinkman().size());//发送sql
  ```

  

* 级联延迟

  1. 一对多的一、多对多的```set```标签

     ```xml
     <set name="" [table=""]
          fetch="select" 默认发送select形式的语句
          	   "join"  lazy无效，只发一条左连接sql
          	   "subselect" 发送子查询
          lazy="true" 默认开启延迟，一次发一条sql
          	  "false" 一次发送所有sql
          	  "extra" 延迟，自动简化sql
          ></set>
     ```

  2. 一对多的```many-to-one```标签

     ```xml
     <many-to-one name="" column="" class=""
                  fetch="select" 默认，select格式的sql
                  	   "join" lazy无效，只发一条左连接sql
                  lazy="proxy" 默认，根据一方的class标签的lazy来决定
                  	  "false" 不延迟，一次发送所有sql
     ```

     