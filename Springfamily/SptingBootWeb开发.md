<h1>SpringBootWeb开发</h1>
> 1. 创建springboot应用
>
> 2. 选中需要的模块 Web JDBC Mybatis Redis 等；SpringBoot将这些场景自动配置
> 3. 在配置文件中指定相关配置
> 4. 编写业务代码

自动配置原理

```java
xxxAutoConfiguration:自动向容器添加组件
xxxProperties:封装配置文件
xxxConfigurer :扩展配置
xxxCoustomizer : 自定义配置
```



### 一、静态资源映射规则

1. 所有 /webjars/** 都去classpath:/META-INF/resources/webjars/找资源

   webjars：以jar包的方式引入静态资源；在maven中导入

   http://www.webjars.org/

   ```xml
   <!--导入 jquery-webjars -->
   <dependency>
       <groupId>org.webjars</groupId>
       <artifactId>jquery</artifactId>
       <version>3.3.1</version>
   </dependency>
   ```

   访问地址：/webjars/jquery/3.3.1/jquery.js
   
2. /** 访问当前项目的任何资源

   ```java
   classpath:/META-INF/resources;
   classpath:/resources;
   classpath:/static;
   classpath:/public;
   ```

   访问地址：/xxx/xxx.js

3. 欢迎页面：静态资源中的所有index.html

   访问地址 /

4. 页面图标  **/favicon.ico

##### 修改静态文件夹路径

```properties
spring.resources.static-locations=classpath:/hello/,classpath:/xxx/
```



### 二、模板引擎

将模板和数据交给模板引擎，由模板引擎生成页面

模板引擎：JSP、Freemarker、Thymeleaf等

SpringBoot不支持JSP，推荐使用Thymeleaf

#### 1. 导入Thymeleaf

1. 引入场景启动器：spring-boot-starter-thymeleaf

   ```xml
   <!--修改thymeleaf版本-->
   <properties>
       <thymeleaf.version>
           3.0.9.RELEASE
       </thymeleaf.version>
       <!--布局功能的支持程序 thymeleaf3主程序 layout2以上版本-->
       <thymeleaf-layout-dialect.version>
           2.1.1
       </thymeleaf-layout-dialect.version>
   </properties>
   ```

   

#### 2.Thymeleaf使用

* 把html放在 classpath:templates目录下,thymeleaf自动渲染

* 在html导入thymeleaf名称空间

  ```html
  <html lang="en" xmlns:th="http://thymeleaf.org">
  ```

  

### 3. Thymeleaf语法

* 基本标签

  ```html
  <span th:each="user:${users}">该标签每次遍历都会生成</span>
  ```

  

* 表达式语法

  ```html
  ${} 取值，底层是OGNL；获取属性、调用方法、使用内置对象
  *{} 配合th:object进行使用
  #{} 获取国际化内容
  @{} 配合 th:href 定义url
  ~{} 配合 th:insert
  ```

  

### 修改SpringBoot的默认配置

1. 自动配置组件时，先检查容器中是否已经存在用户自己配置的（@Bean @Component）如果有就使用用户配置，有些组件可以有多个（VeiwResolver）则将用户配置与自动配置组合



### 三、扩展SpringMVC

以前在springmvc.xml中添加intercepter等，现在使用配置类

不能@EnableWebMvc（使用该注解后将全面接管SpringMvc所有springMvc自动配置失效）

* 通过继承WebMvcConfigurationSupport   （自动配置失效）来重写addInterceptor方法

* <font color="green">实现 WebMvcConfigurer 来重写addInterceptor方法，自动配置保留</font>

  ```java
  @Configuration
  public class WebConfig implements WebMvcConfigurer {
      @Autowired
      private LoginInterceptor loginInterceptor;
      @Override
      public void addInterceptors(InterceptorRegistry registry) {
          registry.addInterceptor(loginInterceptor)
                  .addPathPatterns("/**")
                  .excludePathPatterns("/","/login","/index.html","/webjars/**");
      }
  }
  ```


 模板修改后想要实时生效，关闭模板缓存，ctrl+f9重写编译

```properties
spring-thymeleaf.cache = false
```



登录检查拦截器

实现HandlerInterceptor





Restful风格

| 操作 | 普通                  | Restful            |
| ---- | --------------------- | ------------------ |
| 查询 | getEmp                | emps----GET        |
| 添加 | addEmp?xxx            | emp----POST        |
| 修改 | updateEmp?id=xx&xx=xx | emp/{id}---PUT     |
| 删除 | deleteEmp?id=xx       | emp/{id}----DElETE |

```properties
#springBoot 2.0 默认关闭了delete，put请求
spring.mvc.hiddenmethod.filter.enabled=true
```

```html
<button th:attr="uri=@{/emp/}+${em.id}" class="btn btn-danger" id="del">删除</button>
<form id="delFrom" method="post">
    <input type="hidden" name="_method" value="delete"/>
</form>
<script>
    $("#del").click(function () {
        $("#delFrom").attr("action",$(this).attr("uri")).submit();
    })
</script>
```







thymeleaf页面抽取

```xml
在success.html中标注公共片段
<div th:fragment="common"></div>
在error.html中引入该片段
<div th:insert="~{success::common}"></div>
```



### 四、异常处理器

1. 方法一，不自带自适应，浏览器和客户端异常结果是一样的

```java
@ControllerAdvice//声明异常处理器
public class MyExceptionHandler{
    @ExceptionHandler(Exception.class)//处理所有异常
    public String handleException(){
        ...
    }
}
```

2. 方法二，自适应效果，根据浏览器和客户端返回不同结果

   转发到 /error 

```java
@ControllerAdvice
public class MyeExceptionHandler{
    @ExceptionHandler(UserException.class)
    public String userException(HttpServlet request,Exception e){
        request.setAttribute("javax.servlet.error.status_code",500);//自定义状态码，跳转到5xx.html页面
        Map<String,Object> map = new HashMap<>();
        map.put("code","user.notexist");
        return "forward:/error";
    }
}
```

3. 携带定制数据

SpringBoot处理异常的原理：异常转发到 /error 由BasicErrorController来处理，响应的数据由 getErrorAttributes得到（AbstractErrorController规定的方法）

两种实现方法：

* 编写ErrorController的实现类或者AbstracErrorController的子类放在容器中

* springBoot在启用默认的异常处理配置前会检查容器中是否没有用户自定义的错误属性

  ```java
  @Component//将自定义的错误属性加入容器
  public class CustomErrorInfo extends DefaultErrorAttributes {
      @Override
      public Map<String, Object> getErrorAttributes(WebRequest webRequest, boolean includeStackTrace) {
          Map<String, Object> errorAttributes = super.getErrorAttributes(webRequest, includeStackTrace);
          errorAttributes.put("mesg","粗错啦");
          return errorAttributes;
      }
  }
  ```



#### 