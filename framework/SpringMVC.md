<h1>SpringMVC</h1>
<font size=4>表现层（web）接收页面参数并将处理完的结果响应给页面</font>

---

<center>Contents</center>
[TOC]

---

### 一、SpringMVC三大组件与前端控制器 

* 前端控制器：DispatcherServlet

* 三大组件：HandlerMaping、HandlerAdapter、ViewResolver

  如果在springMvc.xml没有显示指定处理器映射器和处理器，SpringMVC就会逐个匹配，效率低；

  视图解析中指定前后缀后，modelAndView对象在设置返回数据的页面时就不需要在指定路径，直接写页面名就可以

---

### 二、SpringMVC使用流程

1. 导包

2. ```WEB-INF/web.xml```配置

   ```xml
   约束
   <web-apps>
       <!--配置前端控制器-->
       <servlet>
           <servlet-name>dispatcher</servlet-name>
           <servlet-class>
               ...AdapterServelt</servlet-class>
           <!--指定配置文件位置-->
           <init-param>
               <param-name>
               contextConfigLocation</param-name>
               <param-value>
               classpath:springMvc.xml</param-value>
           </init-param>
           <!--设置前端控制器被tomcat加载的优先级-->
           <load-on-startup>1</load-on-startup>
       </servlet>
       <servlet-mapping>
           <servlet-name>dispatcher</servlet-name>
           <!--处理所有 .action请求-->
           <url-pattern>*.acton</url-partten>
       </servlet-mapping>
   </web-apps>
   ```

3. ```config/springMvc.xml```配置

   ```xml
   <beans 约束>
       <!--使用注解声明Controller，开启包扫描-->
       <context:component-sacn basepackage="包path">
       </context:component-sacn>
       <!--开启自动配置最新的处理器映射器和处理器-->
       <mvc:annotation-driven></mvc:annotation-driven>
       <!--配置视图解析器解析时的默认前缀和后缀-->
       <bean class="...InternalResourceViewResolver">
           <property name="prefix" value="/jsp/"/>
           <property name="suffux" value=".jsp"/>
       </bean>
   </beans>
   ```

4. 编写Controller

   ```java
   @Controller
   public  class List{
       @RequestMapping('/Itemlist')
       public ModelAndView list(){
           ....;
           ModelAndView m = new ModelAndView();
           //设置要返回的数据
           m.addObject("k",数据对象);
           //返回数据到指定页面，由于配置文件以及指定视图解析的前后缀，这里只写页面名即可
           m.setViewName("list");
       }
   }
   ```

   ---

### 三、☆SpringMVC参数绑定（从页面接收参数） 

​				<font size=4>Controller中方法的参数</font>

* **默认支持的参数类型**

  在Controller的方法中可以加参数，也可以不加

  > HttpServletRequest
  >
  > HttpServletResponse
  >
  > HttpSession
  >
  > Model	：底层是request域，对request域做了扩展,无论转发还是重定向，request域数据都可以使用
  >
  > Map :spring 会对map集合通过 spring中的 class org.springframework.validation.support.BindingAwareModelMap 进行实例化

  ```java
  @RequestMapping("")
  public String edit(HttpServletRequest request[,....]){
      ...;
      //将数据存入request域
      model.addAttribute("",obj);
      return "页面名";
  }
  ```

* **基本数据类型、String类型参数**

  <font size=4>在参数名=页面Input的name属性时，SpringMVC可以自动转换类型</font>

  <font size=2>或者：Controller中的方法参数使用@RequestParam(“Input的name”) 指定该参数接收的值，参数名可以随意：…（@RequestParam(“id”) Integer xxx）</font>

  **get请求乱码**：
  
  > 修改tomcat配置文件 
  >
  > ```xml
  > <Conntector URIEncoding='utf-8'></Conntector>
  > ```
>
  > 或者：getBytes(“ISO8859-8”,“utf-8”)

  **post请求经Controller方法接受后乱码**

```xml
配置post乱码过滤器
<filter>
    <filter-name>characterEncodingFilter
    </filter-name>
    <filter-class>..CharacterEncodingFilter
    </filter-class>
    <init-param>
        <param-name>encoding</param-name>
        <param-value>utf-8</param-value>
    </init-param>
</filter>
<filter-mapping>
    <filter-name></filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>
```

* **直接接收pojo**

  要求页面上Input的name属性必须=pojo的属性名

  ```java
  @Request("")
  public String edit(User user){
      ...;
  }
  ```

* **接受vo**

  创建vo封装要查询的对象

  ```java
  public class Vo{
      private User user;
      getter&setter;
  }
  ```

  页面上input的name属性=Vo的对象属性.属性 [.属性…]

  ```java
  @RequestMapping("")
  public String edit(Vo vo){
      ...;
  }
  ```

* **自定义转换器，转换其他类型**

  例如将页面上格式化的String类型转为Date类型，SpringMVC就可以将该数据装入pojo属性或vo

  1. 新建controller.converter包

  2. 创建转换器实现```Converter<源类型，目标类型>```接口

     ```java
     ... implements Converter<String,Date>{
         try{
             解析;
      //解析方式必须与页面的格式一致
         }
     }
     ```

     ```xml
   写法：
     jsp:
   input value="<fmt:formatDate value="${item.createtime}" pattern="yyyy.MM.dd"/>"
     
     转换器：
     Date date = new SimpleDateFormat("yyyy.MM.dd").parse(s);
     ```
  
  3. 配置转换器：转换器由处理器适配器来调用
  
     springMvc.xml
  
     ```xml
     <bean id='xx' 
   class="..FormattingConversionServiceFactoryBean">
         <property name="converters">
         	<set>
                 <bean 
                  class="转换器名"
             </set>
         </property>
     </bean>
     ```
  
  4. <font color='red'> \<mvc:annotaion-driven  soversion=xx /></font>

---

### 四、☆高级参数绑定

* **数组**

  最好vo新设数组属性，

  ```复选框name=vo中数组属性```，Controller接收vo；

* **List**

  vo新设List属性，

  foreach设置循环状态属性

  ```<C:foreach items var varStatus=“status”>``` 

  ```${status.index}作为索引```

  ```复选框name  = voList泛型属性[${status.index}].泛型属性的属性名```

---

### 五、@RequestMapping注解的使用

* **用在方法上：url映射到该方法**

* **用在Controller类上：窄化请求映射**

  ```防止多个Controller的方法重名,相当于一个文件夹，访问时要加上```

  ```java
  @RequestMaping("/itmes")
  public class ItemsController{
      @RequestMaping("/itemList")
      public String list(){...;}
  }
  ```

  ```访问：…/items/itemList.action```

* **指定要响应的请求方法**

  ```@RequestMapping(value=“/list”,method=“RequestMethod.GET”)```

---

### 六、☆Controller方法返回类型

* **返回 ModelAndView**

  ```modelAndView.addObject(“k”,obj);指定返回页面的数据```

  ```modelAndView.setViewName(“/list.jsp”);指定返回的页面```

* **返回String**

  ```返回普通字符串就是页面名，返回数据通过Model完成```

  ```model.addAttribute('k',obj)```

  <font color="red">跳转到另一个方法</font>

  **请求转发**：浏览器url不变，request域数据可以带入

  ```java
  return "forward:方法url.action";//相对或绝对路径
  ```

  **重定向**：浏览器url改变，request域数据丢失

  <font color="red">model.addAttribute(‘k’,obj)设置的参数无论转发还是重定向都可以继续使用</font>

  ```java
  return "redirect:方法url.action"
  ```

  

* **返回void：不走SpringMVC**

  ```java
  //返回数据
  request.setAttribute('k',obj);
  //返回页面
  request.setAttribute('k',obj);
  request.getRequestDispatcher("/list.jsp").forward(request,response);
  ```

  

---

### 七、SpringMVC异常处理

* 防止项目抛出500等异常信息，在架构级别上控制异常

* 自定义全局异常处理器

  1. 编写自定义异常类

  2. 实现HandlerExceptionResolver接口的类

     构造自定义异常对象，将异常信息转发到异常页面

* 在springMvc.xml配置该bean

---

### 八、图片上传

* tomcat上配置上传域

  <Context docBase=“”

* 页面上传域

  ```html
  <form enctype="multipart/formdata">
      <input type="file" name="上传域"
  </form>
  ```

* Controller接收二进制文件

  SpringMVC提供了接口，导入实现类

  1. 导包：fileupload、io

  2. 配置上传组件：

     ```xml
     <bean id
       class=".CommonsMultipartResolver">
         <!--大小b-->
         <property name="maxUploadSize">
             <value>1024</value>
         </property>
     </bean>
     ```

     

  3. Controller方法中使用MultipartFile接口接收

     

---

### 九、Json数据交互

json：属于压缩格式

* 导包：jakson

* 注解

  > @RequestBody：将页面传入的json数据转为pojo属性
  >
  > @ResponseBody：将pojo对象自动转为json并返回给页面

---

### 十、SpringMVC实现Restful

* 前端控制器匹配 ```/```

  > *.action	匹配所有以.action结尾的请求
  >
  > /				匹配除了.jsp以外的所有请求
  >
  > /*			匹配所有请求

* restful：资源定位及资源操作的风格，url命名风格

  要求url中不能用```？```传参也不能用```.action```

* get请求修改：

  ```传参 x?id=${id} 改为 x/${id}```

  ```方法映射 /xx  改为 /xx/{id} [/{x}]```

  ```参数必须有 @PathVariable("页面参数") Integer id 来接收url参数并填入映射```

---

### 十一、拦截器

* 创建拦截器，实现  HandlerInterceptor接口

* 方法

  1. preHandle：最先执行 

     返回 true 放行；false 拦截

     1. 执行时机：```Controller方法未执行，ModelAndView未返回```

     2. 用途：```登录权限验证```

  2. postHandle

     1. 执行时机：```Controller方法执行，ModelAndView未返回```

     2. 用途：```在此方法中设置全局数据处理```

  3. afterHandle

     1. 执行时机：```Controller方法执行，ModelAndView返回```

     2. 用途：```记录操作日志```

* 配置拦截器：springMvc.xml

  ```xml
  <mvc:interceptoers>
      <mvc:interceptor>
          <!--拦截所有必须使用/**-->
          <mvc:mapping path="/**"/>
          <bean class="" />
      </mvc:interceptor>
  </mvc:interceptores>
  ```

* 多个拦截器的执行顺序与配置顺序有关

---



### SSM框架整合

* dao层
* service层
* controller层
* web.xml