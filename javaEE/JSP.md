### JSP （Java Server Pages）

servlet输出html代码太复杂，jsp：在html中写java

---

JSP API :

​	javax.servlet.jsp包及子包

​	初始化方法：jspInit()

​	销毁方法：jspDestory()

​	service： _jspService()



中文编码：

```jsp
<%@page contentType="text/html;charset=UTF-8" pageEncoding="UTF-8" import="java.util.*,java.sql.*"%>
```

```java
contentType="text/html;charset=UTF-8" 相当于servlet的response.setContentType("text/html;charset=UTF-8");

pageEncoding="UTF-8" jsp页面使用utf-8
    
import="java.util.*" 导入其他类，用逗号分隔
```



* jsp执行过程

  jsp 转换为 servlet

  hello_jsp.java 继承了 HttpJspBase（该类继承HttpServlet）,所以hello_jsp.java是servlet

  ```java
  1.hello.jsp 转译为 hello_jsp.java
  2.hello_jsp.java 编译为 hello_jsp.class
  3.执行hello.jsp ，生产html
  4.通过http将html返回给浏览器
  ```

* jsp构成元素

  * 静态内容：html，css，js等
  * 指令：```以“ <%@ ”开始 以“ %> ”结尾```
  * 表达式：```<%= java表达式 %>```用于输出html  <==> ```<% out.println(“html”);```
  * Scriptlet：```<% Java代码 ；%>```
  * 声明：```<%! 字段或方法; %>```
  * 动作：```<jsp:include page=“另一个页面” />```
  * 注释：```<%– 注释 –%>```
  
* include

  在当前jsp嵌入另一个jsp页面

  两种方式：指令include 和 动作include

  ```jsp
  <%-- 指令include--%>
  <%@ include file="footer.jsp" %>
  指令包含是静态包含，源jsp与目标jsp是同一个Servlet，变量可以共享
  <%-- 动作include--%>
  <jsp:include page="footer.jsp" />
动作包含是 动态包含，源jsp与目标jsp是不同的两个Servlet
  ```

  指令include与动作include的区别
  
  ```jsp
  <%@ include file="footer.jsp" %>
  会将footer.jsp转译到当前jsp中，只产生一个java文件。
  
  <jsp:include page="footer.jsp" />
会将两jsp分别转译，产生两java文件，当前jsp在服务端访问footer.jsp，并将返回的结果嵌入响应。
  ```

  <font color="red">传递参数</font>
  
  ```jsp
  <%@ include file="footer.jsp" %>
  将两个jsp合并，不存在参数问题
  
  <jsp:include page="footer.jsp" /> 是对footer.jsp的独立访问，需要传递参数：
  <jsp:include page="footer.jsp">
      <jsp:param name="name" value="value"/>
  </jsp:include>
  
  footer.jsp使用 request.getParameter("name") 取出参数值
  ```



* 跳转

  客户端跳转，服务端跳转

  ```jsp
  客户端跳转：
  <% response.sendRedirect("hello.jsp");%>
  ```

  ```jsp
  服务端跳转：
  <% request.getRequestDispatcher("hello.jsp").forward(request,response);%>
  或者使用动作
  <jsp:forward page="hello.jsp"/>之后的代码不再执行，request共享
  ```



* Cookie对象（javax.servlet.http.Cookie）

  cookie是浏览器与服务器交互数据的方式。

  cookie有服务器创建，创建好后发给浏览器，保存在用户本地，下次访问该网站时，就把cookie发送给服务器

  设置cookie

  ```jsp
  <%@page contentType="text/html;charset=UTF-8" import="javax.servlet.http.Cookie"%>
  <% 
  	Cookie c = new Cookie("name","value");
  //创建cookie,名字是name,值是value
  	c.setMaxAge(24*60*60);
  //设置cookie可以保存一天,0则表示关闭浏览器就销毁cookie
  	c.setPath("/");
  //表示访问服务器的所有应用都会提交该cookie，“/login”表示仅访问到 /login 时才提交该cookie
  	response.addCookie(c);
  //将cookie保存到浏览器
  %>
  ```

  获取cookie

  ```jsp
  <%@page ...... %>
  <%
  	Cookie[] cookies = request.getCookies();
  //获取客户端发来的cookie字典
  	if( null != cookies){
          for(int d=0;d<cookies.length;d++){
              out.print(cookies[d].getName()+ cookies[d].getValue() + "<br>");
          }
      }
  %>
  ```



* session对象(javax.servlet.http.Cookie)

  Session（会话）：从用户打开浏览器访问网站开始，无论在该网站访问了多少页面，直到用户关闭浏览器，都属于同一个会话。

  session对象保存数据的方式类似Map的键值对；

  创建session

  ```jsp
  <%@page contentType="text/html;charset=UTF-8" pageEncoding="UTF-8" import="javax.servlet.http.Cookie" %>
  <% session.setAttribute("key","value"); %>
  <%-- 设置session--%>
  ```

  获取session

  ```jsp
  <% String value = (String)session.getAttribute("key");%>
  ```

  <font color="red">cookie与session的区别与联系</font>

  ```jsp
  session存放在服务端，带锁的盒子
  cookie存放在客户端，钥匙
  用户首次登录服务器时，服务器生成一个session对象并为用户分配一个对应的JsessionID，其值为24个字符组成的字符串。用户每次提交页面，浏览器都会把JsessionID包含在HTTP头中，提交到服务器，服务器根据JsessionID来区分用户，JsessionID保存在客户端，属于客户端session，体现为cookie。
  ```

  浏览器关闭cookie时，服务器无法获取JsessionID，则服务器每次都要生成新的session。

  ```jsp
  解决办法
  response.encodeURL("getSession.jsp");
  //response.encodeURL把访问的页面的url转换为
  // getSession.jsp;jsessionid=24个字符组成的随机字符串  的url
  <a href="<% response.encodeURL("getSession")%>">跳转到带有jsessionid参数的页面</a>
  ```

  session的有效期

  ```jsp
  tomcat/conf/web.xml中的<session-config>
  ```

  
  
* JSP作用域

  ```jsp
  pageContext;<%-- 作用于当前页面 --%>
  requestContext;<%-- 同一个请求 --%>
  sessionContext;<%-- 当前会话 --%>
  applicationContext;<%-- 全局，所有用户共享 --%>
  ```

  * pageContext

    ```jsp
    通过pageContext.setAttribute("key","value")只能在当前页面访问
    <% pageContext.setAttribute("name","jissi"); %>
    <%=pageContext.getAttribute("name")%>
    ```

  * requestContext

    ```jsp
    request.setAttribute("key","value")
    request.getAttribute("key")
    requestContext表示一次请求，请求结束后就会回收数据
    ```

    requestContext与跳转

    <font color="green">服务器跳转(request.getRequestDispather(""))是同一次请求，可以实现页面间传递数据
    客户端跳转(response.sendRedriect(""))会产生新的请求，不能通过request传递数据</font>

  * sessionContext

    ```java
    //sessionContext在同一会话中可以传递数据，由于不同用户的session不同，所以数据不能在不同用户间传递
    
    pageContext.setAttribute("key","value",pageContext.SESSION_SCOPE);
    pageContext.getAttribute("key",pageContext.SESSION_SCOPE);
    ```

  * applicationContext

    ```java
    //applicationContext：全局参数，所有用户共享同一数据
    //application对象是ServletContext接口的实例，
    
    pageContext.setAttribute("key","value",pageContext_APPLICATION_SCOPE);
    pageContext.getAttribute(......);
    pageContext.removeAttribute(...)
  pageContext.getAttributeScope("name") ,返回参数范围 1,2,3,4
    pageContext.findAttribute("name") 在各个参数范围查找参数，有就返回值，null
    ```
    
  * javax.servlet.jsp.PageContext

    <font size=4>继承自 javax.servelt.jsp.JspContext</font>

    

* jsp的九种隐式对象(局部变量，只能在_jspService方法使用)

  <font size=3px>隐式对象指的是不需要显式定义，直接就可以使用的对象，如：request，response</font>

  | 隐式对象    | 含义                                                |
  | ----------- | --------------------------------------------------- |
  | request     | 请求                                                |
  | response    | 响应                                                |
  | out         | 输出                                                |
  | pageContext | 页面作用域                                          |
  | session     | 会话作用域                                          |
  | application | 全局作用域   getServeltConfig().getServeltContext() |
  | page        | 当前对象：this                                      |
  | config      | 获取web.xml中的初始化参数                           |
  | exception   | 处理异常的页面                                      |

  exception对象

  ```jsp
  <%--异常处理页面 isErrorPage="true"--%>
  <%@page contentType="text/html;charset=UTF-8" pageEncoding="UTF-8" isErrorPage="true"%>
  ```

  ```jsp
  <%-- 其他页面 errorPage="异常处理页面.jsp"--%>
  <%@page 指令 errotPage="异常处理页面.jsp"%>
  ```

  ```
  抛出异常的jsp和处理异常的jsp是请求转发关系，共享请求范围参数
  ```

  

* JSTL  (JSP Standard Tag Library)

  标准标签库  jstl库有core，i18n，fmt，sql等

  jar包：jstl.jar  , standard.jar 放在web/WEB-INF/lib

core

* 在jsp中通过指令设置jstl

  ```jsp
  <%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>
  <%--prefix="c"表示后续使用的标签以 <c: 开头--%>
  ```

* set out remove

  * 使用 set 设置参数，out 获取参数

  ```jsp
  <c:set var="key" value="$('val')" scope="request"/>
  <c:out value="$('key')"/>
  <==>
  <% request.setAttribute("key","val") %>
  <%=request.getAttribute(key) %>
  ```

  ```jsp
  scope（作用域）取值：page,request,session,application
  ```

  * remove 删除参数

  ```jsp
  <c:remove var="key" scope="request"/>
  <==>
  <% request.removeAttribute("key"); %>
  ```

  * if else

  JSTL通过```<c:if test="${ (条件) }">```进行条件判断，没有else，常在<c:if的条件里取反。

  ```<c:if test=“${empty 参数名}”```进行判空(对象为null，str长度为0，set长度为0)

  ```jsp
  <%@page contentType="text/html;charset=UTF-8" pageEncoding="UTF-8" import="java.util.*" %>
  <%@ tablib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>
  <c:set var="age" vlaue="${10}" scope="request"/>
  <c:set var="name" value="${null}" scope="request"/>
  <c:if test="${age<10}">
      <P>xxx</P>
  </c:if>
  <c:if test="${!(age<10)}">
      <p>xxx</p>
  </c:if>
  <c:if test="${empty name}"><p>xxx </p></c:if>
  ```

  

  * choose

  具有else功能的标签

  ```jsp
  <c:choose>
      <c:when test="${}"> xxx </c:when>
      <c:otherwise>xxx</c:otherwise>
  </c:choose>
  ```

  
  * forEach

  jsp中的for循环可读性差，可以使用jstl的forEach替代

  forEach遍历参数，而不是直接遍历数组

  ```jsp
  需要先设置参数
  <% request.setAttribute("参数名",list)%>
  <c:forEach items="${参数名}" var="item" varStatus="st"></c:forEach>
  <%--var用来临时保存参数，varStatus是遍历状态--%>
  ```

* forTokens

  用于字符串拆分，可以指定多个分隔符

  ```jsp
  <c:set var="names" value="a,b|c,d!e" />
  <c:forToken items="${names}" delims=",|!:" var="name">
      <c:out value="${name}" />
  </c:forToken>
  ```

  <font size=4>fmt 标签常用来进行格式化</font>

* fmt:formatNumber  格式化数字

  ```jsp
  <%@ taglib uri="http://java.sun.com/jsp/jstl/fmt" prefix="fmt" %>
  ```

  ```jsp
  <fmt:formatNumber  表示格式化数字
  minFractionDigits="2" 表示至少保留两位小数
  maxFractionDigits="5" 表示最多保留5位小数
  ```

  ```jsp
  <fmt:formatNumber type="number" value="${1.001}" minFractionDigits="4" />
  ```

  

  * fmt:formatDate  格式化日期

  ```jsp
  <% Data now = new Date();%>
  <fmt:formatDate value="${now}" patten="yyyy年MM月dd日"/>
  ```

  ```jsp
  yyyy年 MM月 dd日 E星期 a上下午 HH时 mm分 ss秒 S毫秒 z时区
  ```

  fn标签

```jsp
<%@ taglib uri="http://java.sun.com/jsp/jstl/functions" %>
```

```java
${fn:trim(str)} //去除string两边的空格并返回
${fn:toLowerCase(str)} //改为小写  toUpperCase(str)
...
```



* EL表达式

  ```jsp
  <%@page 指令 isELIgnored="false" %>打开tomcat的EL支持
  ```

  * 取值

    ```jsp
    在jstl中取值：<c:out value="${name}" />
    EL     取值：${name}
    ```

  * 作用域优先级

    对于 pageContext , requset , session , application 各个作用域都有name属性时，EL取值优先级：

    ```jsp
    pageContext > requset > session > application
    ```

  * <font color="red">JavaBean</font>

    javaBean的标准：

    ```java
    1.提供无参 public 的构造方法
    2.每个属性都有 public 的 getter 和 setter//设置属性的方法，如 publci setName()、getName()
    3.如果属性是 boolean 则对应 is 和 setter 方法
    ```

    * 获取javaBean的属性

      ```java
      //注意 hero是参数名，其值为一个对象
      ${hero.name} //相当于自动调用 public getName()
      //属性是 boolean型 时，自动调用 isXXX()方法
      ```

  * 取参

    EL表达式获取浏览器传来的参数

    ```java
    http://127.0.0.1/login.jsp?name=abc
    ${param.name} //取出参数name
    <==> request.getAttribute("name")
    ```

  * eq 判断

    ```jsp
    ${age ge 18?"成年"："未成年"}
    ```
  
    ```java
    eq，ne|neq: 等于，不等于
    gt,lt  : 大于，小于
    gte|ge,lte|le: 大于等于，小于等于
    not 非
    mod 求模
    is [not] div by 能否被整除
    is [not] even 是否为偶数
    is [not] odd 是否为奇数
    ```
  
    
  
    
  
    