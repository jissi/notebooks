### Servlet

Servlet本身不能运行，需要在一个web应用中运行，web应用部署在tomcat中

---

* servlet结构：查看xmind文件
  
* Servlet常用对象
  
  ```
  ServletRequest,HttpServletRequest
  ServletResponse,HttpServletResponse
  ServeltConfig		初始化参数信息及ServletContext对象
  ServletContext		访问容器为当前web应用提供的各种资源
  ```
  
  **对于每一个应用，Servlet容器会创建一个ServletContext对象。这个对象中封装了上下文（应用程序）的环境详情。每个应用程序只有一个ServletContext。每个Servlet对象也都有一个封装Servlet配置的ServletConfig对象。**
  
* 开发servlet步骤
  
  * 创建web应用项目
  * 编写servlet
  
* 部署到tomcat
  
* 文件结构

  项目名：j2ee

  j2ee/web/WEB-INF/classes/xxx.class

  j2ee/web/WEB-INF/web.xml
  
  j2ee/web/xxx.html

---

* web应用创建

  新建java项目，不要包名，添加servlet.api.jar(tomcat lib目录)

  新建文件夹：j2ee/web/WEB-INF/classes

  设置java编译路径：classes/

  新建xml文件：WEB-INF/web.xml

* 新建 HelloServlet.class

  ```java
  import java.io.IOException;
  import javax.servlet.http.HttpServlet;
  import javax.servlet.http.HttpServletRequest;
  import javax.servlet.http.HttpServletResponse;
  public class HelloServlet extends HttpServlet{
      public void doGet(HttpRequest request,
                       HttpResponse response){
          try{
              response.getWriter().println("<h1></h1>")
                  //向客户端响应 <h1></h1>
          }catch(IOException e){}
      }
  }
  ```

  编写web.xml实现servlet到html的映射

  ```xml
  <?xml version="1.0" encoding="UTF-8" ?>
  <web-app>
      <!--映射HelloServlet到/hello-->
      <servlet>
          <servlet-name>HelloServlet</servlet-name>
          <servlet-class>HelloServlet</servlet-class>
      </servlet>
      <servlet-mapping>
          <servlet-name>HelloServlet</servlet-name>
          <url-pattern>/hello</url-pattern>
      </servlet-mapping>
  </web-app>
  ```

* 部署：修改tomcat的server.xml或移动项目到指定位置

---

### Servlet访问流程

1. 首先访问```http://127.0.0.1:8080/login.html```

   打开一个静态页面，该页面通过form，以post的形式提交数据

2. 提交数据到```/login```路径，并且附带```method=“post”```

3. tomcat收到新的请求：```http://127.0.0.1:8080/login```,其路径是```/login```，接着到配置文件```web.xml```进行匹配，```/login```对应的Servlet类是```LoginServlet```

4. tomcat找到LoginServlet后发现没有实例，调用LoginServlet的public无参数的构造方法LoginServlet（）实例化一个对象

5. 根据提交的信息带有```method=“post”```调用```doPost```方法

6. doPost方法参数request对象来提取数据

7. doPost方法参数response对象设置响应，servlet的工作结束

8. tomcat将html返回给浏览器



----

### doGet()、doPost、service（）

* service()会自动判断是get还是post，并调用doGet()或doPost()

* 浏览器以get方式提交，使用doGet()处理

  ```
  get方式：
  form的默认方式
  通过一个超链接访问地址
  在地址栏直接输入地址
  ajax指定使用get
  ```

  

* 浏览器以post方式提交，使用doPost()处理

  ```
  post方式：
  form设置method="post"
  ajax指定post
  ```

---

* 中文处理

  * 获取中文参数

    ```
    1. html：提交数据使用UTF-8编码
    	<meta http-equiv="Content-Type" content="text/html;charset=UTF-8">
    2. html：提交方式为post
    	form的method="post"
    3. 在servlet进行编码解码
    	byte[] b = name.getBytes("ISO-8859-1")
    	name = new String(b,"UTF-8")
    	或者request.setCharacterEncoding("UTF-8")放在
    	request.getParameter("name")之前
    ```

  * 返回中文响应

    在servlet中加上：

    ​	```response.setContentType(“text/html;charset=UTF-8”);```

---

### Servlet生命周期

实例化 –>初始化 —> 提供服务 –> 销毁 –> 被回收

* 实例化：构造方法

  用户通过浏览器访问一个路径，该路径被相应的servlet调用，该servlet就会被实例化，（单实例：无论servlet被访问多少次，构造方法只执行一次）

* 初始化：init（）

  LoginServlet继承了HttpServlet,同时继承了init（ServletConfig）方法

  init是一个实例方法，只执行一次。初始化相关参数

* 提供服务：service()

  service()方法判断调用用doGet()还是doPost()来处理数据

* 销毁：destroy（）

  * server.xml中设置了  reloadable=“true”

    ```xml
    <Context path="/" docBase="..." debug="0" reloadable="true" />
    ```

    reloadable=“true”意味着有任何类被更新，web应用就会自动重启，重启是调用destroy（）

  * 关闭tomcat的时候，destroy（）会被调用

* 被回收

---

### 页面跳转

登录成功或失败后跳转到不同页面

跳转方式：服务端跳转和客户端跳转

* 服务端跳转：浏览器地址不变

  request.getRequestDispatcher(“xxx.html”).forward(request,response)

  由服务器解析xxx.html并响应给客户端

* 客户端跳转：浏览地址改变

  request.sendRedirect(“xxx.html”)

  直接将目的网址响应给客户端

---

### srvlet自启动

要求servlet随着tomcat一起启动进行初始化，以便提前检查数据库等

，但是servlet只有在用户第一次访问是才能进入生命周期。

* servlet实现自启动

  web.xml中，在需要自启动的servlet处，添加

  ```xml
  <load-on-startup>10</load-on-startup>
  <!---数字代表优先级，取值1-99，越小越优先-->
  ```

  并且，要在需要自启动的servlet中提供init(ServletConfig)方法

---

### 常用方法

* request常用方法

  ```java
  request.getRequestURL()//请求的完整URL，包括协议,主机名,端口
  request.getRequestURI()//请求的资源：如/hello
  request.getQueryString()//以get方式请求的参数
  request.getRemoteAddr()//客户机ip
  request.getRemoteHost()//客户机主机名
  request.getRemotePost()//客户机端口
  request.getLocalAddr()//服务器ip
  request.getLocalName()//服务器主机名
  request.getMethod()//客户机请求方式
  ```

  获取参数：

  ```java
  request.getParameter("参数名")//获取单值参数值
  request.getParameterValues()//获取多值参数值，返回str数组
  request.getParameterMap()//遍历索引参数，返回Map类型
  ```

  关于Map型数据的处理

  ```java
  Map即是字典
  Map<String,String[]> map = request.getParameterMap()
      //该字典的key为参数名，value为对应的参数值列表
      //java.tuil.Map;java.util.Set
  Set<String> names = map.keySet()
      //将map的key值组成一个集合
  for(String name:names){//通过key遍历map
      System.out.println(Arrays.toString(map.get(name)))
  }
  ```

  获取请求头：

  ```java
  request.getHeaderNames()//返回所有头信息名称，
  request.getHeader("key")//获取请求头
  	//如：key为user-agent,则返回浏览器名称
      /*
      host：
      user-agent：
      accept：接受的数据类型
      accept-language
      accept-encoding：接受的压缩方式
      connection：是否保持连接
      cache-control：缓存时限
      */
  ```



* response方法

  设置响应内容

  ```java
  PrintWriter pw = response.getWriter()//获取PrintWriter
      //java.io.PrintWriter
  pw.println("")
     append()
     format()等
  ```

  设置响应格式

  ```java
  response.setContentType("text/html;charset=UTF-8")
      //text/html即是格式，对应request.getHeader("accept")
      //换成lol/lol浏览器无法识别，就会弹窗选择打开方式或保存
      
  ```

  设置响应编码

  ```java
  response.setContenType("text/html;charset=UTF-8")
      //以utf-8编码发送，并通知浏览器使用utf-8
  response.setCharacterEncoding("UTF-8")
      //仅仅以utf-8发送
  ```

  客户端跳转

  ```
  302跳转：临时跳转
  	response.sendRedirect("xxx.html")
  301跳转：永久跳转
  	response.setStatus(301)
  	response.setHeader("Location","xxx.html")
  ```

  设置缓存：使用缓存可以加快页面加载，降低服务器负担，可能看到过时信息

  ```java
  //关闭缓存
  response.setDateHeader("Expires",0)
  response.setHeader("Cache-Control","no-cache")
  response.setHeader("pragma","no-cache")
  ```

---

### 下载文件

```
ServletContext对象的 getSourceAsStream("path")来读取本地文件作为输入流
response.getOutputStream()获取输出流
响应头：
 Content-Type:application/force-download
 Content-Length:String.valueOf(in.available())
 Content-Disposition:attachment;filename=保存为
```



### 上传

*注意*：get不能上传，form的method=“post”,还要加上```enctype=“multipart/form-data”```表示提交二进制文件

```html
<form action="uploadPhoto" method="post" enctype="multipart/form-data"> 
</form>
```

需要``<input type=“file”``字段进行上传

需要两个第三方包：commons-io-1.4.jar、commons-fileupload-1.2.2.jar 放在WEB-INF/lib/

```java
org.apache.commons.fileupload 包负责文件上传
org.apache.commons.io 包负责输入输出

主要接口 FileItemFactory  , FileItem
主要类   DiskFileItemFactory , DiskFileItem , ServletFileUpload
```

```java
fileupload包把请求正文包含的复合表单中的每个子部分都看作一个FileItem对象
	普通文本框，提交按钮等都是formFiled
	上传域是非formFiled
```

接口及关系：思维导图

```java
//主要代码
DiskFileItemFactory factory = new DiskFileItemFactory();
factory.setSizeThreshold(1024);//设置缓冲区大小1K
ServletFileUpload upload = new ServletFileUpload(factory);
upload.setSizeMax(1024*1024);//设置文件最大1M
List items = upload.parseRequest(req);//将请求正文（即上传表单）的所有数据解析为FileItem对象列表
Iterator iter = items.iterator();//列表构成迭代器
while(iter.hasNext()){
    FileItem item = (FileItem) iter.next();
    if(item.isFormFiled){//判断当前FileItem是否为普通表单
        //对普通表单数据的处理
        String name = iterm.getFieldName();//获取名称
        String value = item.getString();//获取值
    }else{
        String name = iterm.getName();//获取文件path
        iterm.write(File file);//将文件写入文件
    }
}
```





### javax.servlet.http.Cookie

<font size=4>每个cookie对象包含一个cookie名和cookie值</font>

```java
Cookie cookie = new Cookie("name","value");
cookie.setMaxAge(0);//设置cookie有效期，0s即client删除cookie，>0则可以保存到硬盘，<0则只能在当前有效
response.addCookie(cookie);//将cookie添加到响应结果，发送到客户端
Cookies[] cookies = request.getCookies();//获取请求中的cookie数组
cookie.getName();
cookie.getValue();
cookie.setValue("value");
cookie.setMaxAge(0);//告诉浏览器删除cookie
cookie.setDomain(".rhel.com");//仅www.rhel.com服务器可以访问本cookie
cookie.setPath("/");// 服务器跟路径，该服务器所有web应用都能访问本cookie
cookie.setPath("/login/");//仅仅Login应用可以访问cookie
```



----

### Web工作目录

<font size=4>Tomcat为web应用提供的默认工作目录  <catalina_home> /work/ [engineName] / [hostname] /[contextPath]</font>

<font size=4>配置web应用的\<Context> 元素时，workDir=工作目录</font>

<font size=4>容器初始化web应用时，会在刚创建的ServletContext对象中设置 “javax.servelt.context.tempdir”属性 ，其值为 File对象，代表当前工作目录</font>

本机workdir： ```/home/jissi/.IntelliJIdea2018.1/system/tomcat/Unnamed_Servlet/work/Catalina/localhost/ROOT```

```java
File tempdir = (File) context.getAttribute("javax....")
```

---

### 转发与包含

<font size=4>Servlet对象不能直接相互调用</font>

web组件间协作的两种途径：

* **请求转发**：Servlet源组件先对请求预处理，再转发给其他web组件， 由其他组件来完成响应等或后续操作
* **包含**：        Servlet源组件把其他组件生成的响应结果包含到自己的响应结果中

源组件与目标组件处理的是同一请求,共享response和request；

目标组件可以是 Servlet，jsp，html；

<font color="green">转发与包含都依赖 javax.servlet.RequestDispatcher接口</font>（请求分发器）

```java
//RequestDispatcher对象的获取途径：
	1.ServletContext对象 getRequestDispatcher("目标组件绝对path")
    2.ServletRequest对象 getRequestDispatcher("目标组件相对|绝对path")
```

```java
//RequestDispatcher实例方法
//throws IOException,ServletException
forward(request,response);//转发到目标组件
//forward方法会先清空响应正文的缓冲区，所以源Servlet的信息被清空
//目标组件是Servlet或jsp就调用service方法，是html就直接发送
//如果转发之前源组件就提交了响应，如调用response.flushBuffer(),或者跳转前调用的pw.close(),forward会抛出IllegalState异常
//之后的代码依然执行
include(request,response);//包含目标组件的响应
//将目标组件的响应结果作为自己响应的一部分
```

---

### 重定向

<font size=4>客户端请求url，服务端返回302（让客户端访问另一个web组件），客户端访问另一个web组件，客户端收到另一组件的响应</font>

```java
HttpServeltRespone接口的 sendRedirect("url")
```

```java
1.源组件的响应结果不会发送到客户端，如果源组件在重定向之前发送了响应，sendRedirect就会异常。
2.源组件重定向之后的代码依然被执行。
3.源组件与目标组件不共享request，即不能共享请求范围内的数据。
```

---

### 访问容器内的其他web应用

<font size=4>每个web应用都有一个ServletContext对象</font>

```java
ServeltContext接口的 getServeltContext("url") 即可获取其他web应用的ServletContext对象
```

```java
tomcat的  <Context>元素的 crossContext属性设置了能否访问其他web应用，默认false
 crossContext=false  // 不允许访问其他web应用,调用getServletContext返回 null
 crossContext=true	 // 允许访问其他web应用的ServletContext
```



---

### 并发问题

<font size=4>多个客户端访问同一Servlet时，通常会为每个客户端分配单独的线程，这些线程并发执行同一Servlet的service()方法</font>

列如使用全局变量时，容易导致参数值出错（先来的线程修改值后进入延时，而后来的线程修改了值，导致先来线程输出值出错）

* 合理定义变量作用域

* 使用java线程同步机制（与se中写法不同）

  ```java
  ...
  public void service(...) throws...{
      synchronized(this){
          对数据操作
      }
  }
  ```

  

* 使用javax.servlet.SingleThreadModel接口（已被废弃）

  Servlet实现该接口，则可以采用两种方式解决之一来运行

  ```java
  1. 在任意时刻只能有一个线程指定service方法，其他客户排队等待
  2. 容器为每个servlet创建对象池，对象池中存放了Servlet的多个实例，容器为每个请求分配一个线程，并将对象池中空闲的servlet实例分配个该线程。（tomcat采用该方法）
  ```

  