<h3>JSP访问JavaBean</h3>
---

<font size=4>java程序放到JavaBean中，在jsp中通过标签访问JavaBean可以简化jsp代码</font>

<center>Contents</center>
[TOC]



### JavaBean

javaBean的特性:

* JavaBean是一个public类
* 有一个不带参数的构造方法
* 通过setter和getter来设置和获取属性（setAttr()，getAttr()）
* 实现java.io.Serializable接口就能够被持久化

### JSP访问JavaBean

```jsp
<%--导入javaBean类--%>
<%@ page contextType="text/html;charset=UTF-8" import="test.ConterBean" %>

<%--声明JavaBean对象,设置类名，class文件，作用范围-->
<jsp:useBean id="取名" class="test.ConterBean" scope="session" /> 

<%--访问JavaBean属性,并作为响应返回-->
<jsp:getProperty name="名称" property="属性名" />
    等价于：<%=实例名.get属性() %>
```

JavaBean的范围

```java
scope取值：
page:默认值，当前页面范围
session:会话范围
request:请求范围
application:应用范围
pageContext.getAttributesScope("attr")	//返回属性的范围
```

