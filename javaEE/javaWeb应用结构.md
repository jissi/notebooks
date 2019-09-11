### Java Web应用目录结构

---

* java web 应用的内容

  ```
  Servlet组件：标准的servlet实现类
  JSP组件：	  包含java代码的HTML文档
  相关的java类：
  静态文档：    html，图片，声音等不需要动态生成的文件
  客户端类：	 有客户端来运行的类 如Applet
  web.xml:	 配置文件，必须位于 WEB-INF目录下
  ```

* 目录结构

  ```
  /project/					根目录
  /project/WEB-INF/			存放web应用的配置文件
  /project/WEB-INF/classes/	存放各种.class文件
  /project/WEB-lib/			运行web应用需要的jar文件
  /project/自定义目录/			 存放jsp，html等文件
  
  IDE中
  /project/src/				源码
  ```

  