<h1>Springboot开发Blog</h1>
[TOC]

### 1. 需求分析-原型设计

### 2.前端开发

* semanticUI
* 插件集成：markdown支持，语法高亮，动画，滚动监听，目录生成，滚动优化



### 3.后端开发

##### 框架搭建

* springboot模块导入：web，jpa，dev tool，aop，thymeleaf

##### thymeleaf版本更改(不要修改)

```xml
<!--修改thymeleaf版本为3-->
<--<thymeleaf.version>3.0.9.RELEASE</thymeleaf.version>
<thymeleaf-layout-dialect.version>2.1.1</thymeleaf-layout-dialect.version>-->
```

* 配置

  ```yml
  spring:
  #thymeleaf 模式默认html或者html5
    thymeleaf:
      mode: HTML
  ```

  

##### JAP配置

```yml
#数据源配置
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://localhost:3306?Blog?characterEncoding=UTF-8
    username: jissi
    password: jissi
#jpa配置
  jpa:
    hibernate:
#    自动同步实体类结构，实体类固定后要改为none，使用create会每次都重建表
      ddl-auto: update
    show-sql: true
```



##### 日志配置

* 配置文件

  ```yml
  #日志配置
  logging:
  #全局使用
    #level: info
    level:
    #指定包日志
      root: info
      com.jissi.blog: debug
    #日志输出
    file: 
    	name: log/blog.log
  ```

* 自定义日志输出配置：logback-spring.xml

  ```xml
  <?xml version="1.0" encoding="utf-8" ?>
  <!--logback 自定义配置-->
  <!--
      日志格式：
      blog.2020-03-08.0 10MB
      blog.2020-03-08.1 10MB
      blog.2020-03-08.2 10MB
      ....
  -->
  <configuration>
      <!--保持springboot默认的日志配置-->
      <include resource="org/springframework/boot/logging/logback/defaults.xml" />
      <property name="LOG_FILE" value="${LOG_FILE:-${LOG_PATH:-${LOG_TEMP:-${java.io.tmpdir:-/tmp}}/}spring.log}"/>
      <include resource="org/springframework/boot/logging/logback/console-appender.xml" />
      <!--重写 file-appender.xml-->
      <appender name="TIME_FILE" class="ch.qos.logback.core.rolling.RollingFileAppender">
          <encoder>
              <pattern>${FILE_LOG_PATTERN}</pattern>
          </encoder>
          <file>${LOG_FILE}</file>
          <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
              <!--日志命名格式-->
              <fileNamePattern>${LOG_FILE}.%d{yyyy-MM-dd}.%i</fileNamePattern>
              <!--保留一个月-->
              <maxHistory>30</maxHistory>
              <!--springboot 默认10M切分日志，修改为10M-->
              <timeBasedFileNamingAndTriggeringPolicy class="ch.qos.logback.core.rolling.SizeAndTimeBasedFNATP">
                  <maxFileSize>10MB</maxFileSize>
              </timeBasedFileNamingAndTriggeringPolicy>
          </rollingPolicy>
      </appender>
  
      <root level="INFO">
          <!--输出到控制台和文件-->
          <appender-ref ref="CONSOLE" />
          <appender-ref ref="TIME_FILE" />
      </root>
  </configuration>
  ```



##### 异常处理

resources/templates/error/404.html

* error
* 404\500

默认位置：resources/static/静态 、   resources/template/动态

全局异常页面

```html
<!--转义符 <-- 输出的异常信息 使用 th:remove="tag"移除标签，只有查看网页源码才能看到 -->
<div>
    <div th:utext="'&lt;!--'" th:remove="tag"></div>
    <div th:utext="'RequestURL: '+ ${url}" th:remove="tag"></div>
    <div th:utext="'Exception message : '+ ${exception.message}" th:remove="tag"></div>
    <div th:remove="tag">
        <li th:each="st : ${exception.stackTrace}" th:remove="tag">
            <span th:utext="${st}" th:remove="tag"></span>
        </li>
    </div>
    <div th:utext="'--&lt;'" th:remove="tag"></div>
</div>
```

* 自定义404异常

  ```java
  @ResponseStatus(HttpStatus.NOT_FOUND)//标记该异常，可用于全局异常判断
  public class NotFoundException extends RuntimeException{
      public NotFoundException() {
      }
      public NotFoundException(String message) {
          super(message);
      }
      public NotFoundException(String message, Throwable cause) {
          super(message, cause);
      }
  }
  ```



* 全局异常处理

  ```java
  @ControllerAdvice
  public class ControllerExceptionHandler {
      private Logger logger = LoggerFactory.getLogger(this.getClass());
      @ExceptionHandler(Exception.class)
      public ModelAndView exceptionHandler(HttpServletRequest request,Exception e) throws Exception {
          //记录日志
          logger.error("request URL: {}, Exception: {}",request.getRequestURL(),e);
          //如果异常类是自定义的404异常,交给springboot处理
          if (null != AnnotationUtils.findAnnotation(e.getClass(), ResponseStatus.class)){
              throw e;
          }
          ModelAndView mv = new ModelAndView();
          mv.addObject("url",request.getRequestURL());
          mv.addObject("exception",e);
          mv.setViewName("error/error");
          return mv;
      }
  }
  ```





### 使用Aop进行日志处理

记录访问的url、访问者ip、方法、参数、返回的内容

* 定义切面类

  ```java
  @Aspect
  @Component
  public class LogAspect {
      private final Logger logger = LoggerFactory.getLogger(this.getClass());
      //定义所有切点
      @Pointcut("execution(* com.jissi.blog.web.*.*(..))")
      public void log(){}
      //前置通知
      @Before("log()")
      public void doBefore(JoinPoint joinPoint){
          //通过joinPoint可以拿到当前切点信息
      }
      //拿到方法的返回值 result
      @AfterReturning(returning = "result",pointcut = "log()")
      public void doAfter(Object result){}
      //封装内部类日志对象，用来保存信息
  }
  ```
  
  



### 页面导入

thymeleaf静态访问路径：默认在static路径下，不用写static

使用 th:fragment="名称(参数)" 抽取公共片段

将标签内容作为参数替换 th:replace="片段文件 :: 名称(~{::标签})"

将直接传值参数替换 th:replace="片段文件 :: 名称(值)"



### 实体类设计

实体类关系：

<img src="/run/media/jissi/other/data/notebooks/resources/blog-entity.png" style="zoom:45%;" />



评论还要关联评论：存在父子关系

* 博客类 Blog 关系维护方

  > 对象属性：分类对象、用户对象、List(标签)、List(评论)
  >
  > 基本属性：标题、内容、图片、标记、浏览次数、赞赏开启、[推荐开启、]版权开启、评论开启、发布状态、时间、更新时间

* 分类 （方便管理） Type

  > 名称

* 标签（方便管理）Tag

  > 名称

* 评论类 Comment

  > 昵称、邮箱、内容、头像、创建时间

* 用户 User

  > 昵称、用户名、密码、邮箱、类型、头像、创建时间、更新时间



### 引用分层

* 终端显示层
* 请求处理层
* 业务逻辑层
* 持久层
* 数据库

### 评论功能

* 回复
* 列表
* 列表嵌套  : 只做两级 一个评论对象包含一个父评论和所有子孙评论列表
* 管理员回复：根据当前session中是否有用户



### 使用国际化来方便替换静态资源

* 全局配置

  在resource下建立 messages.properties ，thymeleaf使用 #{}取值

  ```yml
  # 修改默认文件名
  spring: 
    messages:
      basename: il8n/messages
  ```

  

* 中文配置 messages_zh_CN.properties

  ```properties
  nav.home = 首页
  ```

  

* 英文配置 messages_en_US.properties

  ```properties
  nav.home = home
  ```

  