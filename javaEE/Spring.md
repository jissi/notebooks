### Spring

---

<font seize=4>Spring是一个基于IOC和AOP结构的将j2ee系统的框架</font>

<font size=4>IOC  （Inversion Of Control）  ：反转控制，是Spring的基础。创建对象由原来的 手动   new 构造方法  变成了交由Spring创建对象</font>

<font size=4>DI （Dependency Inject） ：依赖注入，对象的属性已经被注入相关值，可以直接使用</font>

AOP (Aspect Oriented Program) : 面向切面编程

---

[TOC]



### <font size=4>导入包的方法</font>

```
1. 新建spring项目（Java project类型）
2. 将lib.rar 解压到 project/spring/lib/
3. 导入包：右键project -> properties -> java build path -> libaries -> add external jars
4. 编写类及其属性、getter() 和 setter（）方法
```

### <font color='red' size="5"> src/applicationContext.xml 是Spring的核心配置文件</font>

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:aop="http://www.springframework.org/schema/aop"
    xmlns:tx="http://www.springframework.org/schema/tx"
    xmlns:context="http://www.springframework.org/schema/context"
    xsi:schemaLocation="
   http://www.springframework.org/schema/beans
   http://www.springframework.org/schema/beans/spring-beans-3.0.xsd
   http://www.springframework.org/schema/aop
   http://www.springframework.org/schema/aop/spring-aop-3.0.xsd
   http://www.springframework.org/schema/tx
   http://www.springframework.org/schema/tx/spring-tx-3.0.xsd
   http://www.springframework.org/schema/context     
   http://www.springframework.org/schema/context/spring-context-3.0.xsd">
    <bean name="c" class="com.xxx.Category">
        <property name="name" value="category 1"/>
    </bean>
</beans>
通过关键字 c 来获取 Category对象 ， 该对象被获取的时候就被注入了字符串 "category 1" 到 name属性
```

---

### 通过spring来获取对象

```java
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;
import com.spring.Category;//自定义的类

    
    ApplicationContext context = new ClassPathXmlApplicationContext(
    new String[] {"applicationContext.xml"}  );
    Category category = (Category) context.getBean("c");

```

---

### 注入对象

类将对象作为属性时（又有对该对象的 getter和setter），可以注入对象使用 ref属性

```xml
<property name="category" ref="c" />  c为上述的Category对象，ref用于注入对象
```

---

### 使用注解

* 注入对象

  1. 在applicationContext.xml的\<bean>标签之前加上

     ```xml
     <context:annotation-config /> 通知spring使用注解方式配置
     ```

  2. 在类中将需要注入的对象属性前加上 @Autowired

     ```java
     @Autowired
     private Category cate;
     //也可以将@Autowired写在 setter之前
     ```

  3.  或者使用 @Resource(name=“C”) 
  
     ```C 是 Category在applicationContext.xml中的 name属性值```
  
     ```java
     @Autowired 先按类查找，再按name属性查找  即 byType -> byName
     @Resource(name="")  直接按name属性匹配 即 byName
     ```
  
     
  
* 对Bean的注解

  1. 在applicationContext.xml中不再写\<bean> 标签,只写

     ```xml
     <context:component-scan base-package="com.pojo" />
     告诉Spring bean都放在com.pojo 包
     ```

  2. 在类上使用 @Component(“name”)注解,表明该类是bean，其name值为name

     ```java
     @Component("C")
     public class Category{}
     ```

  3. 变量的初始化：声明变量时给定初始值

     ```java
     private int id = 0;
     ```

  ---

### AOP

  <font size=4>面向切面的编程思想将功能分为   核心业务功能 和 周边功能</font>

  <font size=4>核心业务：登录，增删数据等</font>

  <font size=4>周边功能：性能统计，日志，事务管理等</font>

  <font size=4>周边功能在AOP中被定义为切面</font>

  <font size=4>AOP：核心业务与切面功能分别独立开发，最后再编制到一起</font>

* 编写主业务类 Service

* 编写切面功能类 Aspect

  ```java
  package pojo
  public class Aspect{
      public void fun(ProceedingJoinPoint joinPoint) throws Throwable{
          ...//切面左边的功能
          Object obj = joinPoint.procees();//执行业务
          ...//切面右边的功能     
      }
  }
  ```

  <font color="red">切面功能类的方法必须有(ProceedingJoinPoint joinPoint) 参数，而且方法体要有 Object obj = joinPoint.proceed() 才能执行主业务</font>

* applicationContext.xml配置文件

  ```xml
  <!--声明业务类-->
  <bean name="s" class="pojo.Service"></bean>
  <!--声明切面功能-->
  <bean id="定义切面功能类名" class="pojo.Aspect"></bean>
  <aop:config><!--切面配置-->
      <!--切入点配置：id,在切入点执行业务所有方法-->
      <aop:pointcut id="定义切入点名" express="execution(* pojo.Service.*(..))"/> 
      <!--切面配置，id，要添加的切面功能-->
      <aop:aspect id="切面名" ref="切面功能类名">
          <!--切面环绕切点，在执行切点业务的前后执行切面功能-->
          <aop:around method="切面功能类的方法" pointcut-ref="切入点名"/>
      </aop:aspect>
  </aop:config>
  ```

  关于 ```express=“execution(* pojo.Service.*(..))”```，第一个*表示任意类型，第二个* *表示任意方法。对pojo.Service开头的任意类型的方法进行切面

---

### 注解AOP

* 使用 @Component(“s”)注解 Service类

* 配置注解切面类

  ```java
  @Aspect 注解切面类
  @Component("aspect") 注解切面类为bean
  
  注解切面方法
  @Around(value="execution(* pojo.Service.*(..))") 对Service类中的所有方法进行切面
  ```

* applicationContext.xml 只写

  ```xml
  <context:component-scan base-package="pojo"/>
  <aop:aspectj-autoproxy />
  ```

---

### 注解方式测试

* 导入 junit和hamcrest包

* 修改Test类

  ```java
  @RunWith(SpringJUnit4ClassRunner.class)//该类是spring测试类
  @ContextConfiguration("classpath:applicationContext.xm;")//Spring配置文件位置
  
  @Autowired 装配对象
  Category C;
  @Test
  public void test(){
      ...
  }
  
  ```

  