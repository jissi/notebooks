### IntelliJ IDEA

----

[TOC]

---

### 配置文件位置

  ```
  默认个人配置文件位置：~/.IntellJIdea2017.2
  ```

  修改配置文件位置

  ```shell
  Idea安装位置/bin/idea.properties  存放了idea全局配置信息
  
  # vim idea.properties
  idea.config.path=修改后的路径/.IntellJIdea/config
  idea.system.path=修改后的路径/.IntellJIdea/system
  ```

---



### 创建java项目

  ```shell
  1. 点击 Create New Project 
  2. 左侧提供了很多项目，java是默认的，右侧是可以采用的框架
  ```

* 显示工具栏和工具按钮

  ```shell
  View - 勾选ToolBar 和 Tool Buttons
  ```

* 项目结构

  ```shell
  显示工具栏和工具按钮之后，工具栏中 扳手图标的右边第一个，单击设置项目结构
  ```

* 设置JDK

  ```shell
  点击 项目结构 ，点击左侧project 设置JDK
  ```

* <font color="red">导入第三方 jar</font>

  ```shell
  在 项目根目录下创建 lib 目录,并将jar复制到lib/
  右键lib目录，Add as Library
  ```



---

### 新建web项目

  ```shell
  File - New - Project - Web Application
  ```

* 新建运行环境目录

  ```shell
  右键WEB-INF - New - Directory
  在WEB-INF下新建 classes 和 lib 目录
  ```

* 新建Servlet

  ```shell
  需要第三个包 将 servlet-api.jar 复制到 WEB-INF/lib/
  右键src目录 - New - Servlet
  ```

* 导入jar包

  ```shell
  点击project structure -> Libraries -> + -> Java -> WEB-INF/lib/servlet-api.jar
  ```

* 指定编译的class文件输出目录

  ```shell
  project structure -> project compiler output -> 设置为 WEB-INF/classes
  ```

* 向web.xml添加映射

  ```xml
  <?xml version="1.0" encoding="UTF-8" ?>
  <web-app>
      <servlet>
          <servlet-name>HelloServlet</servlet-name>
          <servlet-class>HelloServlet</servlet-class>
      </servlet>
      <servlet-mapping>
          <servlet-name>HeloServlet</servlet-name>
          <url-pattern>/hello</url-pattern>
      </servlet-mapping>
  </web-app>
  ```

* 配置tomcat

  ```shell
  1. 工具栏倒三角 - Edit Configurations
  2. + - 左侧选择 tomcat server - Local
  3. 点击 Configure..
     Server - Tomcat Home: tomcat安装路径
  4. Deployment -> + -> Artifact.. 自动生成 xxx.war
  ```



---

### Springboot

* 创建项目

  ```java
  1. New - Project - Spring Initializr
  
  2. 设置项目参数
  	Grop:com.java
  	Artifact:springboot
  3. 选择web模块
  	选择web，勾选web
  4. 指定项目路径
  ```

  

### Debug模式

* 断点

  在调试模式下，当代码运行到断点位置时就会停下来。

  设置方法：

  ```java
  两种
  1. 将光标移到需要设置断点的行，run - toggle line breakpoint
  2. 在行号的右侧单击鼠标（ctrl+f8）
  ```

* 开始调试

  点击debug按钮

### 常用快捷键

* 查找

  | 快捷键           | 功能                       |
  | ---------------- | -------------------------- |
  | 双击 shift       | 在项目目录中查找文件       |
  | ctrl + f         | 在当前文件查找文本         |
  | ctrl + shift + f | 整个项目中查找文本         |
  | ctrl + n         | 查找类                     |
  | ctrl + shift + n | 查找文件                   |
  | ctrl + e         | 最近文件                   |
  | alt + f7         | 函数，变量，类被引用的地方 |

* 编辑快捷键

  | 快捷键                      | 功能                        |
  | --------------------------- | --------------------------- |
  | shift + enter               | 换行                        |
  | Ctrl + r                    | 替换当前文件的内容          |
  | ctrl + shift + r            | 替换项目内容                |
  | shift + f6                  | 重命名类，方法，变量        |
  | ctrl + d                    | 复制当前行到下一行          |
  | ctrl + x                    | 剪切当前行                  |
  | ctrl + shift + z            | 连续撤销                    |
  | ctrl + t                    | 更新代码                    |
  | alt + insert                | 生成构造器，getter,setter等 |
  | alt + enter                 | 自动修复                    |
  | ctrl + alt + L              | 格式化                      |
  | ctrl + /   ctrl + shift + / | 单行，多行注释              |
  | ctrl + alt + v              | 补全返回值                  |
  | ctrl + alt + t              | 添加 surround with          |
  |                             |                             |
  |                             |                             |
  |                             |                             |
  |                             |                             |
  |                             |                             |

### Maven配置（参见 javaEE/Maven.md）

### Git配置

* git安装配置（参见 软件说明书/Git.md）

* 配置

  ```shell
  file - settings - version control - git | github
  ```

* pull 项目到本地

  ```shell
  菜单 - vcs - checkout from version control - git
  输入项目参数 - clone
  ```

* 将本地项目push到github

  ```shell
  1. 在github新建仓库
  2. 在本地创建仓库
  	vcs - import into... - create git repository - 
  3. 将项目加入本地仓库
  	右键项目 - git - add 
  	右键项目 - git - commit
  ```

  