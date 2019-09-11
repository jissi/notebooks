### tomcat

本机安装目录：/usr/local/src/tomcat/

* Apache与tomcat的区别

  Apache是web服务器，tomcat的java服务器

  tomcat只是一个servlet容器，可以认为是Apache的扩展

  Apache是html容器，tomcat是jsp/servlet容器

  

* tomcat 工作流程

  ```
  1. 客户端发送要访问Servlet的请求
  2. Servlet容器接收请求并解析
  3. Servlet容器创建 ServletRequest对象(包含 http请求信息)
  4. Servlet容器创建 ServletResponse对象(用来生成http响应)
  5. Servlet容器调用 servlet的 service(ServeltRequest对象,ServletResponse对象) 来处理请求
  6. servlet从ServelrRequest对象获取http请求
  7. servlet利用ServletResponse对象生成http响应
  8. Servlet容器把servlet生成的响应发送给客户端
  ```



* Tomcat 各组件的关系（server.xml文件结构）

  ```xml
  <Server>
      顶层元素，代表整个Servlet容器组件，包含多个<Service>
      <Serice>
          顶层元素,包含一个<Engine>,以及多个<Connector>元素
          <Connector>
              负责接收请求，返回响应
          </Connector>
          <Engine>
              处理所有Connector收到的请求，有多个<Host>
              <Host>
                  定义一个虚拟主机，包含多个web应用，有多个<Context>
                  <Context>
                     代表了虚拟主机上的一个web应用
                  </Context>
              </Host>
          </Engine>
      </Serice>
  </Server>
  ```



* **Context元素**

  ```xml
  属性：
  path="指定web应用的url入口"
  docBase="web应用的根目录，或war文件的路径"
  className="实现Context组件的java类名"，默认为 
      org.apache.catalina.core.StandardContext
  reloadable="true" 监视.class ,web.xml文件的改动并自动重新加载
  ```

* tomcat 启动文件

  ```shell
  cd /usr/local/src/tomcat/apache/
  ./startup.sh  <==> catalina start
  ./shutdown.sh <==> catalina stop
  ./catalina run 可以显示tomcat控制台
  ```

* 端口号：8080

  查看端口信息 netstat 

  ​						lsof -i:8080

  端口配置信息位于：server.xml

  修改配置后重启tomcat

* 配置项目：

  在server.xml的Host标签之间，添加如下标签

  ```java
  < Context path="/" docBase="/home/jissi/j2ee/web" reloadable="true" />
  ```

* tomcat 会在WEB-INF/classes目录下找class文件

