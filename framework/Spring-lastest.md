<h1>Spring</h1>

spring核心功能：IOC/DI、AOP、声明式事务

core container：core、beans、expression、context

---

[TOC]

---

#### 1. IOC：控制反转

​	完成原来由程序员主动实例化对象的事情

​	控制：控制类的对象，反转：交由Spring管理

​	**解耦：解除了对象管理和程序员之间的耦合**

* 核心包：core、beans、expression、context、commons-logging

* 配置文件：applicationContext.xml  配置内容最终存储到Spring容器```ApplicationContext```

  配置文件基于schema(.xsd文件)，可以理解为DTD(xml语法检测)的升级版，比DTD具有更好的扩展性

  ```xml
  <bean id="people" class="com.ioc.Entity.People">
      <property name="name" value="Abi"/>
      <property name="age" value="20"/>
  </bean>
  ```

* 默认配置文件加载时就创建对象

  ```java
  ApplicationContext是个接口，测试时一般使用其实现类：ClassPathXmlApplicationContext；
  -----------------------------------
  ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
  context.getBean("id名"[,要转为某个类型.class]);
  context.getBeanDefinitionNames();//拿到所有id
  ```

##### Spring创建对象的方式：构造方法、实例工厂、静态工厂

1. 构造方法

   无参(默认)

   有参：

   index、name、type参数可以精确指定构造方法，如果不精确指定默认是最后一个构造方法

   ```xml
   <bean ...>
       <constructor-arg 
         [index="第几个参数"]
         name="参数名"
         [type="参数类型"]
         value="值"|ref="对象"
   </bean>
   ```

   工厂模式：调用工厂来生成实例，一个工厂可以生成多个对象

2. 实例工厂：需要先创建工厂实例，才能生产对象

   将创建实例的方法设置为实例方法

   ```xml
   <!--实例工厂-->
   <bean id="factory" class="com.ioc.PeopleFactory"/>
   <!--由工厂创建对象-->
   <bean id="a" factory-bean="factory" factory-method="newInstance"/>
   ```

3. 静态工厂：不需要创建工厂实例

   将创建实例的方法设为静态方法

   ```xml
   <!--静态工厂-->
   <bean id="b" class="com.ioc.PeopleFactory" factory-method="newInstance2"/>
   ```

##### 属性注入：构造方法注入、设置注入（Set方法注入）

* 构造方法注入

  ```xml
  <bean id="p" class="xxx">
      <constructor-arg name="name" value="aBi"/>
  </bean>
  ```

* 设置注入：走set方法

  ```xml
  如果属性是Properti类型
  <property name="props">
      <props key="xx">value</props>
      <props key="xx">value</props>
  </property>
  ```

  ```xml
  <bean id="" class="xxx">
      赋值基本类型
      <property name="name" value="aBi"/>
  </bean>
  ```

  ```xml
  赋值另一个bean
  <property name="obj" ref="p"/>
  ```

  ```xml
  赋值Set
  <property name="sets">
      <set>
          <value>1</value>//基本类型
          <value>2</value>
          ...
          <ref>obj</ref>//引用类型
      </set>
  </property>
  ```

  ```xml
  赋值list
  <property name="list">
      <list>...</list>
  </property>
   赋值list只有一个值时可以
  <property name="list" value="1"/>
  ```

  ```xml
  赋值数组
  <property name="array">
      <array>
          <value>1</value>...
      </array>
  </property>
  当数组只有一个值
  <property name="array" value="1"/>
  ```

  ```xml
  赋值map
  <property name="map">
      <map>
          <entry key="a" value="1"/>
          <entry key="b" value="2"/>
          ...
      </map>
  </property>
  ```

#### 2.DI 依赖注入

当一个类中的属性A需要依赖另一个对象B时，将B赋值给属性A的过程叫依赖注入（使用ref来赋值）

```xml
<bena id="B" class="xxx"/>
<bean id="x" class="yyy">
    <property name="A" ref="B"/>引用另一个对象
</bean>
```







