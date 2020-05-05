



---

[TOC]

---



#### IOC

* 注解

  ```xml
  <import resource=""/><!--加载其他spring配置-->
  <!--加载外部properties配置文件-->
  <context:property-placeholder location="classpath:config.properties"/>
  <!--组件扫描-->
  <context:component-scan base-package="com.aop.annotaionAOP"/>
  
  <!--声明式事务-->
<!--spring 事务管理器 适用于 jdbc\mybaits-->
  <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
      <property name="dataSource" ref="dataSource"/>
  </bean>
  <tx:annotaion-drien transaction-manager="平台事务实现类"/>
  ```
  
  

#### 数据库连接

* 连接池

```xml
<context:property-placeholder location="classpath:config.properties"/>
<!--声明式事务-->
<!--连接池-->
<bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
    <property name="driverClassName" value="com.mysql.jdbc.Driver"/>
    <property name="url" value="${jdbc.url}"/>
    <property name="name" value="${jdbc.name}"/>
    <property name="password" value="${jdbc.password}"/>
</bean>
```

* mybatis

  核心配置文件 mybatis.xml schema

  ```xml
  <?xml version="1.0" encoding="UTF-8" ?>
  <!DOCTYPE configuration
          PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
          "http://mybatis.org/dtd/mybatis-3-config.dtd">
  <configuration>
  </configuration>
  ```

```xml
<!--sqlSessionFactory-->
<bean id="sessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
    <property name="dataSource" ref="dataSource"/>
    <property name="configLocation" value="classpath:mybatis.xml"/>
</bean>
<!--mapper扫描-->
<bean id="mapperScanner" class="org.mybatis.spring.mapper.MapperScannerConfigurer">
    <property name="basePackage" value="com.aop.advice.*"/>
</bean>
```





#### 声明式事务

* 注解方式

```xml
<tx:annotaion-drien  transaction-manager="平台事务实现类"/>
```



* 配置文件方式

```xml
<!--定义切面  需要通知的切点-->
<aop:config>
    <aop:pointcut id="point" expression="execution(* com.transaction.UserServiceImpl.*(..))"/>
    <aop:advisor advice-ref="txAdvice" pointcut-ref="point"/>
</aop:config>

<!--指定需要事务控制的切点名-->
<tx:advice id="txAdvice" transaction-manager="transactionManager">
    <tx:attributes>
        <!--执行insert()切点方法时进行事务控制，可以使用通配符 ins*\del*\update*-->
        <tx:method name="insert" 
                   propagation=""
                   roll-back/>
    </tx:attributes>
</tx:advice>
```

