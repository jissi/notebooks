### 配置嵌入式Servlet容器

[TOC]



SpringBoot默认使用Tomat作为嵌入式Servlet容器

#### 1. 定制和修改Servlet容器的相关配置

>a. ServerProperties
>
>> 修改 server.port = 8080 等与server相关的配置
>>
>> server.tomcat.xxx
>
>b. 添加 EmbeddedServletContainerCustomizer 组件  (2.0版本中已经被 WebServerFactoryCustomizer 替代)
>
>```java
>    @Bean
>    public WebServerFactoryCustomizer<ConfigurableWebServerFactory> customizer(){
>        return new WebServerFactoryCustomizer<ConfigurableWebServerFactory>() {
>            @Override
>            public void customize(ConfigurableWebServerFactory factory) {
>                factory.setPort(8080);
>            }
>        };
>    }
>```
>
>

#### 2. 注册Servlet三大组件：Servlet,Filter,Listener

由于没有web.xml文件，springBoot使用以下方式注册组件

* ServletRegistrationBean

  ```java
  //自定义一个Servlet 继承 HttpServlet 重写 doPost 和 doGet方法；
  // 然后向容器添加组件
      @Bean
      public ServletRegistrationBean myServlet(){
          return new ServletRegistrationBean(new myServlet(),"/myServlet");
      }
  ```

  

* FilterRegistrationBean

  ```java
  // 自定义 filter 实现 javax.servlet.Filter接口 重写init,doFilter,destroy方法
      @Override
      public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
     System.out.println("_______________________>Filter执行");
    //放行
    filterChain.doFilter(servletRequest,servletResponse);
      }
  ```

  

* ServletListenerRegistrationBean

  ```java
  // 自定义 监听器 继承 ServletContextListenner 来监听web应用启动和销毁，重写相应的方法
      @Bean 
      public ServletListenerRegistrationBean registrationBean(){
          return new ServletListenerRegistrationBean<MyListener>(new MyListener());
      }
  ```

* SpringBoot 的 DispatacherServletAutoConfiguration 自动添加了 DispatcherServlet组件

  <font color="green">spring.mvc.servlet.path= 可以修改前端控制器拦截路径</font>

  >默认拦截 / 所有请求包括静态资源但不包含jsp
  >
  >> / * 包括jsp

  

#### 3. 其他嵌入式Servlet容器

Jetty  适用于长连接（长时间连接 如 web聊天）

Undertow  并发性能好，不支持JSP

* 打开 ConfigurableWebServletFactory 的继承关系树，可以看到实现该接口的 所有嵌入式Servlet的工厂

* 默认使用tomcat的原因：

  ```xml
  spring-boot-starter-web 依赖包中 有 spring-boot-starter-tomcat
  ```

* 切换其他Servlet容器需要先排除 spring-boot-starter-tomcat 然后手动导入依赖



#### 4. 外置Servlet容器支持JSP

创建项目时选择打war包，生成webapp目录和web.xml

>jar 包形式：执行springboot应用主类，启动IOC容器，创建嵌入式Servlet
>
>war包形式：启动服务器，服务器启动SpringBoot应用，启动IOC容器

1. 创建war项目

2. 将其嵌入式的tomcat指定为provided

   ```xml
   <dependency>
   <groupId>org.springframework.boot</groupId>
   <artifactId>spring-boot-starter-tomcat</artifactId>
   <scope>provided</scope>
   </dependency>
   ```

3. jsp视图解析器访问路径的前后缀

   ```properties
   spring.mvc.view.prefix=/WEB-INF/
   pring.mvc.view.sufix=.jsp
   ```

4. 编写 SpirngBootInitaulizer的子类，重写configure方法 传入主程序类

   ```java
       @Override
       protected SpringApplicationBuilder configure(SpringApplicationBuilder application) {
           return application.sources(SpringBootJspApplication.class);
       }
   ```

   