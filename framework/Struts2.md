<h2>Struts 2</h2>
*本质上相当于Servlet，MVC中的Controller*

常见Web层框架：Struts2、SpringMVC

---

<center>Contents</center>
[TOC]

---

### 一、前端控制器模式

1. Struts核心过滤器必须配置
2. 启动服务器时，过滤器初始化，加载配置文件
3. 请求到达过滤器，过滤器将其交由具体的Action处理

---

### 二、编写步骤

1. 写jsp，向```/hello.action```发起请求

2. 导包：解压Struts，将app目录内的blank后缀改为zip并解压，拷贝基本的依赖

3. 配置前端控制器

   * 在WEB-INF/web.xml配置核心过滤器，任何请求都要先经过过滤器，过滤器负责将请求发送到某个Action

     ```xml
     <web-app>
         <filter>
             <filter-name>struts</filter-name>
             <filter-class>....StrutsPrepareAndExecuteFilter</filter-class>
         </filter>
         <filter-mapping>
             <filter-name>struts</filter-name>
             <url-partten>/*</url-partten>
         </filter-mapping>
     </web-app>
     ```

4. 编写Action类

   Action类的编写原则：

   * 文件名：```xxxAction.class```
   * 方法签名：```public String xxx() ```

   ```java
   public class xxxAction{
       public String xxx(){... ; return "字符串"}
   }
   ```

5. 配置Action访问

   ```src/struts.xml```

   ```xml
   约束
   <struts>
       <!--包配置-->
       <package name="自定义包名" 
                namespace="名称空间一般为 /"
                extends="struts-default" 继承struts-default
                >
           <!--配置Ation，假定 a href = /hello.action -->
           <action name="Action访问路径 hello" 
                   class="Action类path" 
                   method="Action方法名/默认execute方法"
                   >
               <!--跳转，返回值为 "null"/ NONE 时可以不写result-->
               <result name="Action方法返回值">
                   跳转/重定向的资源路径</result>
           </action>
       </package>
   </struts>
   ```

   原理：反射机制 action 的类路径取得Class，method取得方法，执行得到返回值

---

### 三、配置文件

* struts.xml结构

  ```xml
  <struts>
    <interceptors>...</interceptors>  
    <global-result>...</global-result>
    <action>...</action>
  </struts>
  ```

  

**过滤器 StrutsPrepareAndExecuteFilter 初始化加载配置文件**

1. 重要配置文件及其加载顺序

   ```xml
   |	org/apache/struts2/default.properties	加载常量
   V	struts-default.xml	所有核心功能
   |	struts.xml	自己配置的action
   V	web.xml	核心过滤器等配置，tomcat启动先加载一次，核心过滤器再加载一次
   ```

2. 分包管理配置文件

   在包内写xxxAction.xml；

   在struts.xml中导入

   ```xml
   <struts> <include file=""/> </struts>
   ```

3. Struts常量配置（后加载覆盖先加载）

   利用后加载覆盖先加载，覆盖default.properties中的常量

   修改方法：

   * 修改struts.xml  （常用）

     ```xml
     <struts>
         <constant name="常量名" value="新常量值"/>
     </struts>
     ```

   * 创建struts.prpperties

   * 修改web.xml

     ```xml
     <web-app>
         <filter>
             <filter-name></filter-name>
             <filter-class></filter-class>
             <init-param>
                 <param-name>常量名</param-name>
                 <param-value>新常量值</param-value>
             </init-param>
         </filter>
         ...
     </web-app>
     ```

---

### 四、Action类的编写

* 编写方式

  1. Action是POJO类：没有继承，没有实现接口

  2. 实现Action接口

     Action接口包含一些常量字符串和一个String execute方法

  3. 继承ActionSupport类（Action接口的实现类） （常用）



### 五、Action访问方式

* 传统配置：一个请求对应一个action标签

  ````xml
  <package name="" namespace="" extends="struts-default">
      <action name="hello" class="...xAction" method="hello"></action>
      <action name="bye" class="...xAction" method="bye"></action>
  </package>
  ````

* 通配符访问方式

  ```xml
  <package name= namespace= extends=>
      <action name="hello_*" 要跳转到 /hello_save.action
              class="...xAction"
              method="{1}" 第一个 * 的位置>
          <result name="success">/success.jsp</result>
          <result name="login">/login.html</result>
      </action>
  </package>
  ```

  ```xml
  <a href="/hello_save.action"></a>时，将action标签 hello_* 匹配为 hello_save
  ```

* 动态方法访问

  先开启动态访问：struts.xml

  ```xml
  <struts>
      <constant name="struts.enable.DynamicMethodInvation" value="true"/>
      <package name= namespace= extends=>
          <action name="hello"  a标签为 /hello!save.action  访问save方法
                  class="...xAction">
              <result name>xxx</result>
          </action>
      </package>
  </struts>
  ```

---

### 六、Servlet API

1. 完全解耦合：ActionContext：Action上下文（运行环境）

   ```java
   ActionContext context = ActionContext.getContext();//获取ActionContext对象
   //返回指定范围内的属性Map
   Map<String,Object> map = getPrameter();//返回请求参数Map
   getSession();
   getApplication();//返回ServleContext中的属性Map
   //向范围内新增属性,map.put("name",obj)
   context.put( , );//向request范围内设置属性
   getSession().put("msg","message");
   getApplication().put( , );
   ```

   遍历所有属性

   ```java
   Map<String,Object> map = context.getSession();
   Set<String> keys = map.keySet();
   for(String key:keys){
       String[] vals = (String[])map.get(key);
       System.out.println(Arrays.toString(vals));
   }
   ```

   

2. 原生Servlet：ServleActionContext：获取HttpServletRequest/Response

   ```java
   HttpServletRequest request = ServletApplicaionContext.getRequest();
   request.setAttribute("msg","message");//设置request属性
   request.getSession().setAttribute( , );//获取Session
   //application范围属性,两种方法
   request.getSession().getServletContext().setAttribute( , );
   ServletActionContext.getServletContext().setAttribute( , );
   //获取response
   HttpServletResponse response = ServletActionContext.getResponse();
   ```

---

### 七、重定向与转发	result 标签的 type属性值  & 全局跳转页面

```<result name=请求资源名 type= dispacher />```默认转发

1. type的值(继承自 struts-default)

   > chain			转发：  action -> jsp
   >
   > dispatcher	转发：action -> action
   >
   > redirect		重定向：action -> jsp
   >
   > redirectAction	重定向：action -> action
   >
   > stream	文件下载

2. 全局跳转页面

   同一个Action包内(package name=“”)，无论哪个方法返回某个值就跳转到指定页面

   ```xml
   <package name="demo" namespace="/" extends="struts-default">
       <!--全局结果页面-->
       <global-result name="login">/login.html</global-result>
       <!--有局部result时，局部优先-->
       <action name="login" class="xxxx.xxx" method="execute">
           <result name="login">/index.html</result>
       </action>
   </package>
   ```

   

---

### 八、数据的封装

两种方式：属性驱动和模型驱动

```请求 -> 核心过滤器 -> 拦截器1 -> 拦截器2 -> … -> action```

默认拦截器栈中的拦截器全部执行，其中：

> params 拦截器负责 属性驱动方式封装数据
>
> modelDriven 拦截器负责 模型驱动方式封装数据

1. **属性驱动**

   ```< input type  name=“name” />```

   * **单独属性：数据封装到Action**

     Action类中声明 ```private String name``` 和setter，getter方法

   * **向JavaBean中封装：数据封装到JavaBean**

     * 编写JavaBean：User

     * Action中声明  private User user 并提供setter和getter

     * **表单的写法**：OGNL表达式

       ```jsp
       <input type name="user.name"/>
       ```

2. **模型驱动**

   * Action实现```ModelDriven```接口，声明并**实例化JavaBean**

     ```java
     extends ActionSupport implements ModelDriven<User>
     private Users user = new User();
     public User getModel(){retun user;}//拦截器调用，将数据封装到user
     public String execute(){ ... return NONE;}
     ```

3. 数据封装到集合，常用List，Map

   默认属性驱动，数据封装到对象，对象封装到集合

   * Action声明集合属性，提供setter和getter

     ```java
     List<User> list
     Map<String,User> map
     ```

   * 页面上

     ```html
     <input type name="list[0].name"/>
     <input type name="map["key1"].name"/>
     ```

     

---

### 九、拦截器

1. 拦截器（AOP思想）

   * 过滤器与拦截器的区别

     过滤器可以拦截所有类型的，拦截器只拦截.action

2. 自定义拦截器

   接口：Interceptor

   实现类：```AbstractActionInterceptor拦截所有action```

   ​                ```MethodFilterInterceptor定义要/不要拦截```

   自定义拦截器后，需要手动引入默认栈

3. 编写拦截器

   * 继承AbstractActionInterceptor

     ```java
     public class DemoInterceptor extends AbstractActionInterceptor{
         public String interceptor(ActionInvoaction invocation) throws Exception{
             //拦截前的增强:对数据加工
             String result = invocation.invoke();//执行下一个拦截器,放行
             //拦截后增强
             return result;
         }
     }
     ```

   * 继承MethodFilterInterceptor

     ```MethodFilterInterceptor的 excludeMethods属性定义了需要放行的action```

     ```java
     xxx extends MethodFilterInterceptor{
         public String  doIntercept(ActionInvocation invocation) throws Exception{
             //判断session中是否有值（用户是否登录）
             if(...){
                 ...;
                 return "login";//result标签匹配到该值后执行操作，拦截
             }
             return invocation.invoke();//进入下一个拦截器，放行
         }
     }
     ```

   * 配置拦截器

     **配置自定义拦截器后，还需要手动引入默认栈拦截器**

     * 方法1：自定义拦截器，引入自定义拦截器和默认栈

       ```xml
       <package name namespace extends>
           <!--自定义拦截器-->
           <interceptors>
               <interceptor name="自定义名" class="类path"/>
           </interceptors>
           <!--定义全局页面，处理返回login的拦截-->
           <global-result>
               <result name="login">/login.html</result>
           </global-result>
           <!--引入拦截器-->
           <action name class method>
               <interceptor-ref name="定义的名字">引入自定义拦截器
          <!--拦截器继承自MethodFilterInterceptor，配置放行的cation-->
                   <param name="excludeMethods">
                       要放行的action方法:login
                   <param>
               </interceptor-ref>
                       
               <interceptor-ref name="defaultStack"/>引入默认栈
               <result name type >...</result>
           </action>
       </package>
       ```

       

     * 方法2：自定义拦截器和默认栈到新栈，引入栈

       ```xml
       <package name namespace extends>
           <interceptors>
               <interceptor name="" class=""/>自定义拦截器
               <interceptor-stack name="自定义栈">自定义栈
                   <interceptor-ref name="自定义拦截器"/>
                   <interceptor-ref name="defaultStack"/>
               </interceptor-stack>
           </interceptors>
           
           <action name class method>
               <interceptor-ref name="自定义栈"/>
           </action>
       </package>
       ```

       

---

### 十、值栈

* 数据中转站

* ValueStack接口：OgnlValueStack实现类

* ##### Action是多例的，每一个请求创建一个Action实例，并创建ActionContext对象和ValueStack对象

* 每个Action有自己的ActionContext对象和ValueStack对象

* Struts框架将ValueStack对象存到request范围，名称为```struts.valueStack```

  ```java
  ValueStack valueStack = request.getAttribute("struts.valueStack")
  ```

* ActionContext中维护ValueStack的引用

  ```java
  ValueStack vs = ActionContext.getContext().getValueStack();
  ```

  ValueStack在请求时创建，ActionConext对象绑定当前线程，每个请求的值栈独立

  

* ##### valueStack结构

  1. ***context***	： Map结构，map<k, map<k,Obj> >

     Struts默认将以下数据压入context栈

     | key：String | value：Map<String,Object>                              |
     | ----------- | ------------------------------------------------------ |
     | request     | ActionContext.getRequest()；返回request范围的属性map   |
     | session     | ActionContext.getSession()；返回session范围的属性map   |
     | application | ActionContext.getApplication();                        |
     | parameters  | ActionContext.getParameters();                         |
     | attr        | ActionContext.findAttribute();全局查找，小范围到大范围 |

     ##### OGNL表达式从context取值需要加‘#’，eg：<S:property value=“#request.uid” />

     

  2. ***root*** 	： ArrayList结构，Obj / map<k,Obj>

     用户可以向root中存取对象/Map<k,v>

     OGNL表达式从root取值时不需要`#`，eg：<S:property value=“user.id”/>

     

* ##### 保存数据到值栈

  存值到root

  ```java
  ValueStack valueStack = ActionContext.getContext().getValueStack();
  valueStack.push(Obj);//向栈顶压入对象，其他对象下移
  valueStack.set("key",Obj);//向栈顶压入Map，如果栈顶已经是map则新增该键值对，其他对象下移
  ```

  存值到Action：Action由Struts框架压入root

  ```java
  在Action类中为其设置属性；其属性会随Action一同进栈
  ```

* ##### 从值栈取值

  Action将值栈转发到jsp；
  
  ```jsp
  <%@ taglib prefix="S" uri="/struts-tags" %>
  <S:debug></S:debug>	查看值栈
  <S:property value="OGNL表达式"/> 取值
  ```
  
  ```java
  OGNL表达式：
  [0]	//root栈中从第一个到最后一个对象
  [1] //root栈中从第二个到最后一个对象
  [0].top //root栈中的第一个对象
  [0].top.name	//第一个对象的name属性的值  省略[0].top 
  name	//从小到大的范围，查找name属性,取值
  [1].top.key_name.name	//第一个map对象的key的值（对象）的name属性
  简写 ：key_name.name
  ```
  
* ##### 遍历集合

  ```jsp
  root栈的第一个对象为 List<User> userList
  <S:iterator value="userList" [var="user"]>
      写var="迭代对象"属性时，迭代对象被压栈到context；取值时弹出
      不写var属性时，迭代对象被压栈到root，取值时弹出
      <S:property value="name" /> 不写var属性
      <S:property value="#user.name"/>写var属性
  </S:iterator>
  ```

* EL表达式取值栈值：先找request范围，再找值栈

  ```jsp
  <%@ taglib prefix="C" uri="...jstl-core" %>
  <C:forEach iterms="${userList}" var="user">
      ${user.name}
  </C:forEach>
  ```

### 十一、OGNL表达式

传统方式：数据存入request，转发到页面

Struts2提供值栈，数据存入值栈

* OGNL表达式：Object-Graph Navigation Language 对象图导航语言

  可以存取对象属性，调用对象方法

* 使用OGNL

  ```jsp
  <%@ taglib prefix="S" uri="struts-tags" %>
  <S:property value="ognl表达式"/>取值
  <S:property value="'string'.length()" />调用方法
  ```

* 特殊符号

  ```java
  #	取context栈中的值，构建集合
  %	"%{user.name}" <==> " 'user.name' " 将OGNL表达式解析为String
  $	在配置文件中取值栈中的值
  ```

  