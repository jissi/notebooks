<h1>JavaConfig</h1>
Spring1.x时代使用xml配置 -> 2.x时代 可以使用注解 -> 3.x到4.x 提供Java配置

---
<center>Contents</center>
[TOC]

---

### 1. 核心注解 @Configuration 和 @Bean

* @Configuration 作用于类上 该类相当于xml

* @Bean 作用在方法上 相当于bean标签

* @ComponentScan 没有指定扫描路径时，默认扫描所在的同级目录及其子目录

  ```java
  //标记该类为配置类 相当于xml文件
  @Configuration
  //包扫描 扫描@Service等组件将其加入IOC容器
  @ComponentScan(basePackages = "com.test.Service")
  public class SpringConfig {
      //标记将改方法的返回对象加入IOC容器
      @Bean
      public UserDao getUserDao(){
          return new UserDao();
      }
  }
  -------------------------------------------------
  //加载配置类
  AnnotationConfigApplicationContext context = new AnnotationConfigApplicationContext(SpringConfig.class);
  context.getBean(xxx.class);
  ```

  

  

### 2.读取指定外部配置文件

* @PropertySource(value={"classpath:xxx.properties",...}) 用在类上

  如果配置文件不存在 可以忽略，加参数： igonreResourceNotFound=true

* @Value(“${name}”) 用在属性上赋值

  ```java
  //指定外部配置文件
  @PropertySource(value = {"classpath:jdbc.properties","classpath:notFound"},ignoreResourceNotFound = true)
  public class TestProps{
      //将配置文件的值注入到属性
      @Value("${url}")
      private String url;
  }
  ```

  



