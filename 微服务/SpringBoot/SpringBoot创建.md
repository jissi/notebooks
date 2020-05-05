<h1>SpringBoot</h1>
基于Spring框架的再封装



---

<center>Contents</center>
[TOC]

---

### 一、SpringBoo特性

> <font size=4>快速创建独立运行的Spring项目及主流框架集成</font>
>
> <font size=4>使用嵌入式Servlet容器，应用无需打包成war再部署到服务器</font>
>
> <font size=4>starters自动依赖与版本控制</font>
>
> <font size=4>大量自动配置，简化开发</font>
>
> <font size=4>无需配置xml，无代码生成</font>
>
> <font size=4>准生产环境的运行时应用监控</font>
>
> <font size=4>与云计算天然集成</font>

---

### 二、SpringBoot入门

#### 简介

>简化Spring应用开发的框架
>
>整个Spring技术的整合
>
>J2EE开发的一站式解决方案

#### 微服务

> 是一种架构风格；
>
> 一个应用应该是一组小型服务；通过http方式互通
>
> 功能单元动态组合；每一个功能单元都可独立替换升级

单体应用（All in one）：所有应用都在一个war包

---

### 三、SpringBoot项目创建流程

https://docs.spring.io/spring-boot/docs/current/reference/html/getting-started.html#getting-started-first-application-run

#### 	创建Maven工程

##### 		1. 导入SpringBoot相关依赖

```xml
springBoot官网，quick star中
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.example</groupId>
    <artifactId>myproject</artifactId>
    <version>0.0.1-SNAPSHOT</version>

    <!-- 继承父工程：配置了编码，jdk版本，资源过滤，插件配置等，继承了dependencies（依赖版本管理）-->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.2.0.RELEASE</version>
    </parent>

    <!-- Add typical dependencies for a web application -->
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
    </dependencies>

    <!-- Package as an executable jar -->
    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>

</project>
```

##### 		2. 编写主程序，用来启动SpringBoot应用

```java
package com.xxx;
//注解声明该类是SpringBoot应用
@SpringBootApplication
public class Hello(){
    public static void main(String[] args){
        //启动
        SpringApplication.run(Hello.class,args);
    }
}
```

##### 		3. 编写Controller：helloController

```java
SpringMVC相关注解
@Controller
@RequestMapping("/xx")
@ResponseBody
...
```

##### 		4. 启动项目：运行主函数

##### 		5.部署：不用打war包

```xml
springboot官网
添加Maven插件
<build>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
        </plugin>
    </plugins>
</build>
```

点开Maven侧边栏->Lifecycle -> package
		打jar包到target目录
		java -jar 包名  启动项目

打成的jar包自动集成了tomcat

---

### 四、解析

#### 1. POM.xml

* 导入父项目：spring-boot-starter-parent

​	其父项目为 spring-boot-dependencies 定义了依赖包的版本号；真正管理spring boot 所有依赖包版本;<font color="green">是springboot的版本仲裁中心</font>

* 导入依赖：spring-boot-starter-web

spring-boot-starter : spring boot场景启动器；导入了web场景启动器

**spring boot 将所有功能场景抽取出来，做成starter，在项目中引入starter，相关的场景依赖就自动导入**

#### 2. 主程序类

**@SpringBootApplication **标识该类为springboot应用的主配置类

> 本身由多个注解构成:
>
> > @SpringBootConfiguration	标注类为Spring配置类
> >
> > @EnableAutoConfiguration	开启自动配置功能



---

### 五、使用 Spring initalizr 快速创建SpringBoot项目

传统的创建方式是先创建maven工程。

从Spring官网联网下载配置

```xml
file - new project - Spring initalizr - jdk版本 - 
next - 设置名称 - dependencies 选择需要的功能
```

@RestController	=  @Controller + @ResponseBody

* 主程序已经生成
* resources文件夹
  * ```static/```	:保存所有静态资源 js css 等
  
  * ```templates/``` :保存所有模板页面（springBoot默认jar包使用嵌入式tomcat，不支持jsp）；可以使用模板引擎（freemarker,thymeleaf）
  
  * ```application.properties``` : springBoot默认配置文件
  
    修改配置文件：
  
    ```
    修改端口号：server.port = 8080
    指定项目访问根路径：server.context-path=/springBoot
    ```



若要更改文档的整体外观，请在“页面布局”选项卡上选择新的“主题”元素。若要更改快速样式库中的可用外观，请使用“更改当，通过从快速样式库中为所选文本选择一种外观，您可以方便地更改文档中所选文本的格式。您还可以使用“开始”选项卡上的其他控件来直接设置文本格式。

### 六、Springboot 配置文件

#### 1. 配置文件名称固定：application.properties 或 application.yml(不支持@PropertySource)

用来修改SpringBoot配置的默认值；

yml (YAML)；yml以数据为中心；xml将大量数据都浪费在标签上；

```yml
yml配置端口写法
server:
  port: 8080
```

#### 2.properties配置文件写法

中文注入会乱码：idea -  file encoding properties文件勾选转为ascall

```properties
person.name = 张三
person.map.k1 = 1
person.map.k2 = 2
person.list = 1,2,3
#指定项目访问根路径 server.context-path=/springBoot 
#开启debug模式	debug=true
```

##### 配置文件占位符或随机数：

```properties
person.name = 张三${random.uuid}
person.age = ${random.int}
person.dog.name = ${person.name}_dog
#如果${person.name}取不到值，就将“${person.name}”作值；
#或者指定取不到时的值 ${person.name:张三} 
```



#### 3. yml语法

* 基本语法

  key:(空格)value:(空格)	表示键值对，空格必须有

  以空格的缩进来控制层级管理，同级左对其

  ```yml
  server:
  	port: 8080
  	path: /hello
  ```

  属性和值大小写敏感

* 值的写法

  * 字面量：数字、字符串、布尔

    k: v: 

    字符串不用加单引号或双引号；

    双引号不转义特殊字符；

    单引号转义特殊字符，特殊字符作为字符串输出；

  * 对象、Map

    k: v: 

    ```yml
     friends: 
    	name: 123
    	age: 18
    ```

    或者行内写法

    ```yaml
    friends: {name: 123,age: 18}
    ```

  * 数组（List、Set）

    使用 - 值 表示数组中的一个元素

    ```yaml
    pets: 
    	- cat
    	- dog
    	
    行内写法
    pets: [cat,dog]
    ```

#### 4.向JavaBean组件注入值

* 添加依赖

  ```xml
  <!--导入配置文件处理器-->
  <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-configuration-processor</artifactId>
      <optional>true</optional>
  </dependency>
  ```

* JavaBean上加注解

  ```java
  @Component//一定要将javaBean加入容器
  @ConfigurationProperties(prefix = "person")//绑定配置文件的person key
  ```

  或者不使用 @ConfigurationProperties：使用@PropertySource({"classpath:xxx.properties",...})在JavaBean的属性上加@Value("字面量\${key}从环境变量、配置文件获取\#{SpEl}")

  ```java
  /**
   *传统的xml配置文件写法
   *<bean class="Person">
   *   <property name="name" 
   *            value="字面量\${key}从环境变量、配置文件获取\#{SpEl}"
   *</bean>
   */
  // 直接在JavaBean的属性上加@Value("字面量\${key}从环境变量、配置文件获取\#{SpEl}")
  @PropertySource(value={"classpath:person.properties"})
  class ...{
  	@Value("${person.name}")
  	private String name;
  }
  ```

* @ConfigurationProperties和@Value的区别

  |                      | @ConfigurationProperties                          | @Value               |
  | -------------------- | ------------------------------------------------- | -------------------- |
  | 功能                 | 批量注入                                          | 一次只能注入一个属性 |
  | 松散语法绑定         | 支持（lastName <-> last-name）                    | 不支持               |
  | SpEL（spring表达式） | 不支持                                            | 支持                 |
  | JSR303数据校验       | Bean加注解 @validated，属性加 @Email 只能注入邮箱 | 不支持               |
  | 复杂类型封装         | 支持                                              | 只能取出基本类型数据 |

  如果只是获取配置文件的值，则可以使用@Value;

  如果专门写了javabean来映射配置文件的值，使用@ConfigurationProperties

* SpringBoot测试

  ```java
  @SpringBootTest
  class SpringBootHelloQuickApplicationTests {
      @Autowired
      Person person;
      @Test
      void contextLoads() {
          System.out.println(person);
      }
  }
  ```

* 其他相关注解

  @ConfigurationProperties和@Value只能获取全局配置文件中的值

  **@PropertySource(value="{classpath:person.properties}") **：类上；加载指定配置文件

  **@ImportResource(locations = {"classpath:bean.xml"})**：加载主类上，导入Spring配置文件，让配置文件里的内容生效;sprintBoot里面没有Spring配置文件，手动编写的配置文件也不能识别,不写该注解则spring配置文件添加的容器无效

#### 5、SpingBoot给容器添加组件

使用注解方式

#### 使用配置类 @Configuration 替代Spring配置文件

​		以前使用```<bean>```标签来添加组件；现在用@Bean

* 写法

  ```java
  //指定改类为配置类
  @Configuration
  public class MyConfig {
       //返回值加入到容器、方法名为容器id
      @Bean
      public HelloService helloService(){
          return new HelloService();
      }
  }
  ```

  

#### 6.SpingBoot Profile指定要使用的配置文件

* 多Profile文件

  多个配置文件写法：<font color="green">application-{profile}.properties/yml</font>

  例如：application-dev.properties/yml、application-prod.properties/yml

  默认会使用application.properties/yml作为主配置文件

* yml文件的文档块来定义profile

  ```yaml
  #三个-划分文档块
  server:
    port: 8080
  spring:
    profiles:
      active: dev #激活dev
  ---
  server:
    port: 8081
  spring:
    profiles: dev
  ---
  server:
    port: 8082
  spring:
    profiles: prod
  ```

  

* 激活指定profile

  1. 在主配置文件指定

  ```properties
  #在主配置文件中设置
  spring.profiles.active=dev
  #激活dev环境配置
  ```

  2. 使用命令行激活

     ```shell
     Program arguments: 
     --spring.profiles.active=dev
     ```

  3. 打包后命令行执行

     ```shell
     java -jar xxxx.jar --spring.profiles.active=dev
     ```

  4. 虚拟机参数

     ```shell
     VM options:
     -Dspring.profiles.active=dev
     ```

#### 7. 配置文件的加载位置

* 默认位置和加载顺序(优先级由高到低；)

* 高优先级覆盖低优先级;配置内容互补

  ```shell
  ./config/
  ./
  classpath:/config/ （resources/config）
  classpath:/			（resources/）
  ```

* 通过 spring.config.location来指定额外配置文件位置

  项目打包以后使用命令行参数启动项目时指定配置文件位置，与默认的位置共同起作用

  ```shell
  java -jar xxxx.jar --spring.config.location=/usr/local/application.properties
  # 配置文件名不叫application时，要手动指定
  java -jar xxxx.jar --spring.config.name=/usr/local/xxx.properties
  ```

* 外部配置加载顺序

  ```shell
  1.命令行参数
  java -jar xxx.jar --server.port=8081 --server.context-path=/abc
  2....
  3.由jar包外向jar包内
  4.优先加载application-{profile}.properties/yml
  ```

#### 8.如何用好SpringBoot

* 如何用好SpringBoot

  > SpringBoot启动会加载大量自动配置类；
  >
  > 需要的功能是否有默认写好的自动配置类；
  >
  > 自动配置类中配置的组件是否有要用的；
  >
  > 给容器中自动配置类添加组件的时候，会从properties类中获取属性，可以在配置文件指定属性的值；

##### 自动配置类在一定条件下才会生效，在配置文件中 ``` debug=true```开启debug模式，控制台会输出哪些自动配置生效：Positive matches

