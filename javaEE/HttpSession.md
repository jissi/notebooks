### Http会话管理

<font sieze=4>Servlet容器为每个会话分配一个HttpSession对象，客户端存放表示SessionID的cookie</font>

---

<font size=4>JSP默认支持会话，Servlet类默认不支持会话</font>

```jsp
<%@ page session="true" %> 显式声明jsp支持session
```

<center>Content</center>
[TOC]



### 访问Session的流程

```java
客户端访问web应用 -- 
    请求中没有表示sessionID的cookie则服务端新建会话对象，并在响应结果中添加表示该sessionID的cookie，客户端将该cookie保存；
    请求中含有表示sessionID的cookie，服务端根据sessionID访问HttpSession对象。
```

---

### HttpSession接口

* 会话范围

  <font size=2>web应用范围与ServletContext对象的生命周期对应，web范围内的共享数据作为ServletContext对象的属性而存在，web组件共享同一ServletContext对象就能共享web应用范围内的数据</font>

  浏览器与web应用进行一次会话的过程，会话范围与HttpSession对象的生命周期对应，web组件共享同一个HttpSession对象，就能共享会话范围内的共享数据

* HttpSession对象的方法

  ```java
  getId()		//返回sessionID
  setAttribute("name",obj)	//设置会话范围内的属性
  getAttribute("name")		//返回对应的属性值
  getAttributeNames()			//返回所有属性名构成的数组
  removeAttribute("name")		//删除属性
  getServletContext()			//返回会话所属的ServletContext对象
  isNew()					//是否是新建的会话
  invalidate()			//销毁当前会话
  setMaxInactiveInterval(int 秒) //设置会话处于不活动的最长时间，超时自动销毁会话，负数表示不限时
  getMaxInactiveInterval()	//返回当前会话可以不活跃的最长时间
  ```

---

### HttpServlet类使用会话

* 设置会话支持

  ```java
  HttpSession session = request.getSession(); 
  ```

  同HttpServletRequest对象来创建/获取会话对象

  ```java
  requset.getSession()	//存在会话则返回会话对象，不存在则创建并返回
  request.getSession(boolean create)//true--相当于getSession() false--不存在就返回null
  ```

---

### 跟踪会话

* 会话的跟踪

  Servlet容器读取客户端请求中包含sessionID的cookie来判断请求来自哪个会话

* 浏览器支持Cookie的情况下，可以将sessionID保存到cookie

* <font color="red">浏览器关闭cookie功能时，通过重写URL来跟踪会话</font>

  把sessionID写到URL中作为参数

  HttpServletResponse对象的 ```public String encodeURL(String url)```

  ```java
  response.encodeURL("login.jsp")工作流程
  1. login.jsp不支持会话则直接响应参数指定的url：login.jsp给客户端
  2. 客户端支持Cookie？响应login.jsp : 响应login.jsp;JSESSIONID=xxxxx
  ```

  

### 会话持久化（将HttpSession对象从内存转移到外存）

<font size="4">会话持久化：将内存中的HttpSession对象保存到文件系统或数据库</font>

<font size=4>优点：节省内存，web应用故障时会话可恢复</font>

持久化对象时，容器不仅会持久化HttpSession对象，还将其所有可序列化（实现java.io.Serializable接口）的属性持久化。

Tomcat会话管理器

```
org.apache.catalina.session.StandardManager类
org.apache.catalina.session.PersistentManager类
帮助文档：tomcat_HOME/webapps/docs/config/manager.html
```

默认使用StandardManager,session被持久化在```/home/jissi/.IntelliJIdea2018.1/system/tomcat/Unnamed_Servlet/work/Catalina/localhost/ROOT/SESSION.ser```

---

### 使用PersistentManager可以更灵活的管理会话持久化

将存储设备称为会话Store。

tomcat中会话Store的接口：```org.apache.Catalina.Store```

实现该接口的类：

```java
org.apache.Catalina.FileStore	//将会话对象保存到文件
org.apache.Catalina.JDBCStore	//将会话对象保存到数据库的一张表中
```

* 配置FileStore

  默认会话对象保存位置：```<Catalina_HOME>/work/Catalina/[host_name]/[application]```

  每个HttpSession对象对应一个 SessionID.ssession文件

  ```xml
  配置META-INF/context.xml
  <Context reloadable="true">
      <Manager className="org.apache.catalina.session.PersistentManager"
               会话管理器名
               saveOnRestart="true"	应用终止时自动保存，重启自动加载session
               maxActiveSession="10"	最多活动的会话数量，超值保存
               minIdleSwap="60"		最小空闲时间，超时保存，-1不限
               maxIdleSwap="120"		最大空闲时间，超时保存
               maxIdleBackup="180"	最大空闲时间，超时备份
               maxInactiveInterval="300">最大空闲时长，超时销毁
          <Store className="org.apache.catalina.session.FileStore"
                 directory="mydir"/>指定会话Store的实现类和保存会话的目录
      </Manager>
  </Context>
  ```
  
  配置JDBCStore<font color="red">失败了</font>
  
  数据库中新建表结构如下
  
  ```sql
  session_id		varchar(100) not null primary key,
  valid_session 	char(1) not null,
  max_inactive	int not null,
  last_access		bigint not null,
  app_name		varchar(255),
  session_data	mediumblob,
  key kapp_name(app_name)
  ```
```

  META-INF/context.xml配置

  ```xml
  <Context>
      <Manager className="org.apache.catalina.session.PersistentManager"
               saveOnRestatr="true"
               maxActiveSession="1"
               maxIdleSwap="120"
               minIdleSwap="60"
               maxIdleBackup="120"
               maxInactiveInterval="200">
          <Store className="org.apache.catalina.session.JDBCStore"
                 driverName="com.mysql.cj.jsbc.Driver"
                 connectionURL="jdbc:mysql://...."
                 sessionTable="sessions"
                 sessionIdCol="session_id"
                 sessionDataCol="session_data"
                 sessionValidCol="valid_sesison"
                 sessionLastAccessedCol="last_access"
                 sessionMaxInactiveCol="max_inactive"
                 sessionAppCol="app_name"
                 checkInterval="60"/>
          
      </Manager>
  </Context>
```

  

### 会话监听

监听会话事件

* HttpSessiontListener接口：监听会话的创建与销毁事件

  ```java
  sessionCreated(HttpSessionEvent event)	//容器创建新会话时触发
  sessionDestoryed(HttpSessionEvent event)
  ```

* HttpSessionAttributeListener接口：监听会话添加，修改，删除属性

  ```java
  attributeAdded(HttpSessionBindingEvent e)
  attributeReplace(...)
  attributeRemove(...)
  ```

* HttpSessionBindingListener接口：监听会话与属性绑定，解绑

  ```java
  valueBound(HttpSessionBindingEvent e)
  valueUnbound(...)
  ```

* HttpSessionActivationListener：监听会话被激活，放置

  ```java
  sessionDidActivate(HttpSessionEvent e)	//激活会话后调用
  sessionWillPassivate(....)			   //放置会话前调用
  ```

* <font color="green">HttpSessionListener和HttpSessionAttributeListener的实现类需要在web.xml中注册</font>

  ```xml
  <web-app>
      <listener>
          <listener-class>实现类</listener-class>
      </listener>
  </web-app>
  ```

  

