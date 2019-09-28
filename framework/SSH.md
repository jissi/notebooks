<h2>SSH框架整合</h2>
Struts2 + Spring + Hibernate

SUN公司三层结构：web层，业务层Service，持久层DAO

---

<center>Contents</center>
[TOC]

---

### 一、各层功能

1. web层：Struts2

   请求 -> 核心过滤器（拦截所有action : /*）  -> 拦截器 -> 目标Action

2. Service：Spring

   ```IOC：解耦合```；```AOP：事务管理，方法增强```
   
3. DAO：Hibernate

   ```ORM（对象关系映射），完成持久化操作```

### 二、配置文件与依赖包

1. 导包

   ```java
   Struts2		：
   struts-2.3.24\apps\struts2-blank\WEB-INF\lib\*.jar
   struts-spring-plugin
   
   Hibernat	：
   hibernate-release-5.0.7.Final\lib\required\*.jar
   slf4j-api,slf4j-log4j,数据库驱动，c3p0连接池
   
   Spring		：
   IOC 4个，AOP 4个 ，spring-jdbc,-tx
   ```

2. 配置文件

   ```xml
   Struts2：
   web.xml 配置核心过滤器
   /src/struts.xml 配置Action
   Hibernate：
   /src/hibernate.cfg.xml	核心配置：数据库，可选配置，映射文件
   packagePath/xxx.hbm.xml	映射文件
   Spring：
   src/applicationContext.xml	Spring核心配置：IOC，AOP
   src/log4j.properties	日志配置文件
   ```

### 三、整合Struts2

两种方法：Struts管理？IOC管理？

***不管哪种方法，先配置web监听器，避免多次加载spring配置***

* ```xml
  web.xml
  <listener><!--写在最前面-->
      <listener-class>...ContextLoaderListener</listener-class>
  </listener>
  <context-param>
      <param-name>contextConfigLocation</param-name>
      <param-value>classpath:applicationContext.xml</param-value>
  </context-param>
  ```

1. Struts管理Action，使用```WebApplicationContext```对象（web工厂方法）手动注入service

2. Struts管理Action，自动注入service

   ```java
   Action的service属性提供setter getter，service由Spring管理
   Struts-spring-plugin包中的配置文件覆盖先前配置，开启根据属性名自动注入对象
   ```

3. <font color="red">Spring管理Action，手动注入service</font>

   ***Action是多例的，配置scope=“prototype”***

   ```xml
   applicationContext.xml添加Action,手动注入对象属性
   <bean id="xxxAction" class="xxxAction" scope="prototype">
       <property name="serviceImp" ref="serviceImp"/>
   </bean>
   struts.xml修改，Action的class属性值为 spring配置的 id
   <package name namespace extends>
       <action name="xx_*" class="id值" method="{1}"/>
   </package>
   ```

   

---

### 四、整合Hibernate

编写Dao，接口+实现类（extends HibernateDaoSupport）

* [Hibernate模板类：```HibernateTemplate```](#template)

  封装了 session ，与JdbcTemplate类似

* HibernateDaoSupport类，dao实现类继承该类

  如同JdbcDaoSupport类：有hibernateTemplate属性，dataSource属性，sessionFactory属性，为sessionFactory属性注入对象，其hibernateTemplate属性就被实例化

  

* HibernateTransactionManager：hibernage事务管器，为其注入sessFactory

  

* ***LocalSessionFactoryBean***：spring提供的类，加载Hibernate配置文件，并创建sessionFactory

  ---

<font color="black" size=5>整合Hibernate</font>

1. 使用hibernate.cfg.xml：LocalSessFactoryBean负责加载配置并创建工厂

   ```xml
   <bean id="sessionFactory" class="...LocalSessionFactoryBean">
       <property name="configLocation" value="classpath:hibernate.cfg.xml"
   </bean>
   ```

2. <font color="red">applicationContext.xml 完成Hibernate核心配置</font>

   ```xml
   <beans>
       <!--配置连接池-->
       <bean id="dataSource" class="...ComboPooledDataSource">
           <property name="classDriver" value=".."/>
       </bean>
       <!--配置sessFactory，并完成Hibernate可选配置,以及映射-->
       <bean id="sessionFactory" class="...LocalSessionFactoryBean">
           <property name="dataSource" ref="dataSource"/>
           <!--hibernate可选配置-->
           <property name="hibernateProperties">
               <props>
                   <prop name="hibernate.dialect">...MySQL5Dialect</prop>
                   ....
               </props>
           </property>
           <!--hibernate映射文件-->
           <property name="mappingResource">
               <list>
                   <value>映射文件位置</value>
                   ...
               </list>
           </property>
       </bean>
       <!--配置平台事务管理器-->
       <bean id="transactionManager" class="...HiernateTransactionManager">
       	<property name="sessionFactory" ref="sessionFactory"/>
       </bean>
       <!--开启声明式事务注解-->
       <tx:annotation-driven transactionManager="transactionManager"/>
       <!--配置dao实现类-->
       <bean id="daoImp" class="dao实现类path">
           <property name="sessionFactory" ref="sessionFactory"/>
       </bean>
   </beans>
   ```

---

### 五、使用延迟加载导致异常

* 使用延迟加载导致异常：no session

* 原因：ssh会话流程

  ```java
  web层发查询请求 -> service层,开启session和事务 -> Dao层，使用了延迟加载，查询
  -> service层拿到代理，结束(session随之关闭) -> web层显示数据->X nosession 
  ```

* 解决方案

  1. 关闭延迟加载：lazy=“false” ；程序性能下降

  2. <font color="red">由web层管理session</font>

     ***在Struts核心过滤器前加上 OpenSessionInViewFilter 过滤器***

     ```xml
     <filter>
         <filter-name>openSessionInViewFilter</filter-name>
         <filter-class>...OpenSessionINViewFilter</filter-class>
     </filter>
     <filter-mapping>
         <filter-name>openSessionInViewFilter</filter-name>
         <url-partten>/*</url-partten>
     </filter-mapping>
     ```

---

### 六、HibernateTemplate模板类实例方法<a name="template"></a>

hibernateTemplate封装了session

* 保存

  ```java
  template.save(ojb);
  template.update(obj);
  ```

* 查询

  ```java
  1. 根据主键查询一个记录
  User user = (User)template.get(User.class,1L);
  2. 查询多条记录
List<User> list = (List<User>) template.find("HQL"[,？代表的参数]);
  3. QBC查询：传入离线查询对象
  List<User> list = (List<User>) template.findByCriteria(detachedCriteria);
  ```
  
* 

### 第一次整合SSH框架出现的问题

1. <font color="red" size=5> 忘记提供setter和getter方法，报错3次！！！</font>
   * service，dao层缺少setter和getter，加载配置文件报错2次
   * action 缺少setter，没能成功封装数据
2. <font color="red">idea web部署不会，导致多次404，还不知道为什么</font>
3. 根据主键查询用户时弄混了HQL

