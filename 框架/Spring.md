<h1>Spring</h1>
Inverse of Control：控制反转

Dependency Inject：依赖注入

Aspect Oriented Programming：面向切面编程

---


<center>Contents</center>
[TOC]

---

### 一、一站式开发

> Struts2（web层）—— Spring（业务层）——Hibernate（持久层）
>
> Spring管理整合Struts2，Hibernate等
>
> 方便解耦，简化开发，事务自动管理

### 二、IOC

> Inverse of Control：控制反转
>
> 对象的创建权交给给Spring，解决程序耦合性高的问题

```mermaid
graph RL
资源对象 --> xml
xml --> 工厂
工厂 --> app
```

> 工厂由Spring提供
>
> xml中记录需要有Spring管理的实现类

6个IOC基本包：

> spring:
>
> ​	core、expression、bean、context
>
> org.apache:
>
> ​	commons：logging
>
> ​	log4

**IOC的编写过程**

1. 下载spring以及其依赖包，并导入

2. 包结构：接口+实现类

3. 将实现类交给Spring

   新建spring配置文件：```src/applicationContext.xml```

   xml约束：```spring包/docs/…reference…./html/xsd….configuration…html最后的bean```

   ```xml
   <?xml version="1.0" encoding=UTF-8?>
   <约束..../>
   <beans>
       <bean id="为实现类指定id" class="实现类的完整路径"></bean>
   </beans>
   ```

4. 使用工厂获取对象

   ```java
   //创建工厂
   ApplicationContext ac = new ClassPathXmlApplicationContext("配置文件名");
   //从工厂获取对象
   接口类 对象 = (实现类) ac.getBean("实现类id");
   //使用对象
   对象.xxx();
   ```

   工厂：```ApplicationContext接口```，```BeanFactory接口（过时）```

   ```
   ApplicationContext接口的实现类：
   	ClassPathXmlApplicationContext	读取src目录的配置文件，加载完就创建对象
   	FileSystemXmlApplicaionContext	读取文件系统，使用getBean时创建对象
   ```

### 三、Spring配置文件

```xml
<约束/>
<import resource="引入配置文件"/>
<beans>
    <bean id="实现类id" 	不能使用特殊字符
          name="实现类名"	与id同效，可以使用特殊字符1
          class="实现类路径"
          scope="singleton默认"	作用范围
          		singleton	单例，运行时只能创建一个对象
          		prototype	多例，每次调用都创建新对象，Action使用多例，线程安全
          		request,session,globalsession(全局web应用)
          init-method="初始化时调用的方法名"
          destroy-method=""
    </bean>
</beans>
```

有多个配置文件时，既可以使用import标签，也可以同时加载多个配置文件：

```java
ApplicationContext ac = new ClassPathXmlApplicationContext("1.xml","2.xml")
```



### 四、DI

> Dependency Inject  依赖注入：
>
> ​	app类需要dao类的方法；spring负责创建dao对象，并将其注入到app

> Spring自动调用setter方法来为对象注入属性值

1. 注入属性

   ```xml
   <bean id="xxx" class="xxx">
       <property name="成员变量名" value="值"/>
   </bean>
   ```

2. 注入对象

   ```xml
   <property name="成员类对象" ref="类对象的id"/>
   ref 来引用
   ```

3. 注入构造方法(默认使用无参数的构造方法)

   ```xml
   <bean id="xxx" class="xxx">
       <constructor-arg name="构造方法参数1" value="值"/>
       <constructor-arg name="构造方法参数2" ref="对象id"/>
       ...
   </bean>
   ```

4. 注入集合

   * 注入数组或List集合

     ```xml
     <property name="arrs">
         <list>
             <value>值1</value>
             <value>值2</value>
             ....
             或者
             <ref bean="对象id"/>
             ...
         </list>
     </property>
     ```

   * 注入Set集合

     ```xml
     <Set>
         <value>值1</value>
         ...
         或
         <ref bean="对象id"/>
         ...
     </Set>
     ```

   * 注入Map集合

     ```xml
     <map>
         <entry key="键" value="值"/>
         ...
         或
         <entry key-ref="" ref="对象id"/>
     </map>
     ```

   * 注入属性文件（Properties类）

     ```xml
     <props>
         <prop key="键">值</prop>
         ...
     </props>
     ```


### 五、IOC使用注解

* 注解的目的：简化配置文件

1. 导入包：spring-aop

2. 接口 + 带注解的实现类

   ```java
   @Component(value="设置id") //默认类名，首字母小写
   public class 实现类名{
       @Value(value="注入值")
       String name;
       @Resource(name="要注入的对象Id")
       Animal dog;
       public void cry(){ ...}
   }
   ```

   <a href="anno">IOC和DI注解</a>

   * 用在类上（IOC）

     > 通用：
     >
     > ​	@Component(value=“id”)		将类交给IOC容器管理
     >
     > ​	@Scope(value=“singleton”)	对象的作用范围
     >
     > 
     >
     > 专用：与@Component相同作用，不能同时使用
     >
     > ​	@Controller(value=“id”)	用于web层
     >
     > ​	@Service(value=“id”)		用于业务层
     >
     > ​	@Repository(value=“id”)	用于持久层

   * 用于注入（DI）

     >用在属性上
     >
     >​	@Value(value=“值”)	注入基本数据类型和String
     >
     >​	@Autowried	自动注入对象（多个对象会出错）
     >
     >根据类id注入对象
     >
     >​	@Autowired + @Qualifier(value=“id”)
     >
     >​	或者使用
     >
     >​	@Resource(name=“id”)	（java提供的注解）

3. 配置文件开启注解扫描

   ```xml
   约束：spring/docs/html/xsd.configuration.html  的 context部分
   <beans ....>
       <context:component-scan base-package="要扫描的包"/>
   </beans>
   ```

4. 编写测试类，获取工厂，获取对象，调用方法

---



### 六、Spring整合JUnit方便测试

需要的包：JUnit4、spring-test

在测试类上加注解

```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration("classpath:applicationContext.xml")
public class SpringTest{
    @Resource(name="要测试的对象id")
    private 测试对象的接口 测试对象;//注入测试对象
    public void run(){...}
}
```

---

### 七、AOP

Aspect Oriented Programmin：面向切面编程

OOP：面向对象编程，AOP是OOP的延续而不是替代

在不修改源码的情况下新增功能

横向抽取机制取代传统纵向继承（高耦合）机制

* AOP原理：代理

  > 为目标对象（需要增强的类）创建代理，代理对象代理的方法被执行就会回调invoke方法，在invoke方法中添加增强功能。

  ```
  不使用AOP：
  	接口 - 实现类 - 测试类直接访问实现类
  使用AOP：
  	接口 - 实现类 - 生成实现类的代理对象 - 测试类获取代理对象并调用代理方法 - 代理方法被执行，自动回调invoke方法（在invoke方法新增代码即是增强功能）
  ```

* 代理方式自动选择：

  > 有接口的情况下：jdk动态代理
  >
  > 无接口的情况下：cglib技术（采用生成类的子类的方式）

* ***Spring AOP术语***

  |         名称 | 说明          | 含义                                                         |
  | -----------: | :------------ | :----------------------------------------------------------- |
  |    joinpoint | 连接点        | 一个类中可以所有方法都可以是连接点，这些方法都可以被增强/拦截 |
  |     pointcut | **切入点**    | 被增强/拦截的方法                                            |
  |       advice | **增强/通知** | 具体的增强（功能）                                           |
  |       target | 目标对象      | 被代理/要增强的对象                                          |
  |      weaving | 织入          | 把增强应用到目标对象的来生成代理的过程                       |
  |       aspect | **切面**      | 切入点+通知                                                  |
  |        proxy | 代理          |                                                              |
  | introduction | 引介          |                                                              |

  **通知需要编写，切入点需要配置**

* **AOP使用流程**

  1. 导包

     ```
     spring核心包、spring-aop、spring-aspect
     com.springsource.org.aopaliance
     com.springsource.org.aspectj.weaver
     ```

  2. 接口 + 实现类（目标对象）

  3. 切面类（编写要增强的功能方法）

  4. spring配置文件

     **切入点表达式**：

     ```
     execution( public void packages.实现类.方法())
     含义：当实现类中的 public void 方法() 执行时，对其增强
     execution( * packages.类.*(..) )
     含义：对实现类中的 任何返回类型 的 任何方法 执行时，对其增强
     ```

     ```xml
     <!--约束 spring配置约束的 aop部分-->
     
     <!--将实现类和切面类交给IOC容器-->
     <beans id="目标对象id" class="目标对象path"></beans>
     <beans id="切面类id" class="切面类path"></beans>
     
     <aop:config><!--aop配置-->
         <aop:aspect ref="切面类id"><!--切面配置-->
             <aop:before method="切面类中的增强方法"
                         pointcut="切入点表达式"/><!--定义通知-->
         </aop:aspect>
     </aop:config>
     ```

     

     