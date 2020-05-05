<h1>Mybatis</h1>
<font size="4">持久层框架,对Jdbc的轻量级封装，适用于用户量大，并发高的互联网项目</font>

---

<center>Contents</center>
[TOC]

---

### Why Mybatis 

* Jdbc存在的问题

  > 1. 频繁创建、释放连接，资源浪费
  >
  > 2. SQL语句固定，不利于维护
  > 3. 解析结果集方法固定

* Hibernate相对重量级，不适合互联网项目

---

### 一、Mybatis结构

* 核心配置：```SqlMapConfig.xml```

* 映射文件：```Pojo.xml```…

* 流程

  > 通过配置文件创建SqlSessionFactory对象；
  >
  > 通过SqlSessionFactory对象获取SqlSession对象；
  >
  > SqlSession对象执行调用方法解析并执行映射文件的sql

* Mybatsi内部对象

  > Executer执行器，执行sql
  >
  > MappedStatement内部封装对象，抽取xml，转换为对象

---

### 二、Mybatis使用流程

1. 导包：核心包+依赖包

2. 创建配置文件

   src同级目录下创建```config```目录，记得添加config为Sources

   ```xml
   新建 config/SqlMapConfig.xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <!DOCTYPE configuration
   PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
   "http://mybatis.org/dtd/mybatis-3-config.dtd">
   <configuration>
       <enviroments default="development">
           <enviroment id="development">
               <!--使用Jdbc事务管理-->
               <transactionManager type="JDBC"/>
               <!--连接池-->
               <dataSource type="POOLED">
                   <property name="driver"
                             value="驱动">
                   </property>
               </dataSource>
           </enviroment>
       </enviroments>
       <!--映射文件-->
       <mappers>
           <mapper resource="映射文件路径"/>
       </mappers>
   </configuration>
   ```

3. 创建Pojo和配置文件

   创建封装数据的pojo：User

   创建```config/User.xml```

   ```xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <!DOCTYPE mapper
   PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
   "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
   <mapper namespace="名称空间，用于区分id,填写mapper接口位置">
       <select id="sql语句标识"
               parameterType="java.lang.Integer"
               传入参数的类型全路径
               resultType="xxx.User"
               返回结果集的类型>
           <!--sql语句-->
           select * from user where id=#{id}
       </select>
   </mapper>
   ```

   核心配置文件中添加该映射文件

4. 测试类

   ```java
   String resource = "User.xml";
   InputStream in = Resources.getResourceAsStream(resource);
   SqlSessionFactory factory = new SqlSessionFactoryBuilder().build(in);
   SqlSession session = factory.openSession();
   User user = session.select("命名空间.id值",参数);
   ```

---

### 三、基本数据库操作

<font color="red">Mybatis自动开启事务，需要手动提交！！！</font>

* CRUD方法

  > session.select(“名称空间.id”，参数)	返回resultType指定的对象
  >
  > session.selectList(“名称空间.id”，)	返回resultType指定的泛型的集合
  >
  > session.update(“名称空间.id”，参数)
  >
  > session.insert(“名称空间.id”，参数)
  >
  > session.delete(“名称空间.id”，参数)

* 根据id查询一条记录

  1. User.xml

     参数说明

     ```java
     id="byID"	//唯一标识
          //传入参数的类型
     parameterType="java.lang.Integer"	
     //返回结果集的类型/泛型
     resultType="com.jissi.User"
     ```

     配置sql

     ```xml
     <select id parameterType resultType>
     	select * from Users where uid=#{id}
     </select>
     ```

     <font color="red">占位符：#{变量名}	:</font>

     > 当传入参数parameterType是基本类型、String时，变量名可以随意；
     >
     > 当传入参数parameterType是pojo类型时，变量名必须是pojo的```属性、属性.属性```

  2. 实现类

     ```java
     User user = session.select("命名空间.id"，参数);
     ```

* 模糊查询多条记录

  1. 配置sql

     返回集合时，返回类型写集合的泛型：Users

     ```xml
     <select id parameterType resultType>
     	select * from Users where uname like
         '%${value}%'
     </select>
     ```

     <font color="red">拼接符：${变量名}：</font>

     > 当传入参数是基本类型/String时，变量名必须为 ```value```
     >
     > 使用拼接符有sql注入(传入的参数为sql关键字，或空格)的风险，只有在like的时候使用

  2. 实现类

     ```java
     List<Users> users = session.selectList(
     	"命名空间.id"，参数);
     ```

* 插入数据

  1. 配置sql

     ```xml
     <insert id="xx"
             parameterType="xx.User">
     	insert into .....
     </insert>
     ```

  2. 实现类

     ```java
     session.insert("...",user对象);
     session.commit();
     ```

* 删除数据

  1. 配置sql

     ```xml
     <delete id="xx"       parameterType="java.lang.Integer">
     	delete from ...
     </delete>      
     ```

  2. 实现类

     ```java
     session.delete("...",主键);
     session.commit()
     ```

* 更新数据

  1. 配置sql

     ```xml
     <update id="xx" parameterType>
     	update ....
     </update>
     ```

  2. 实现类

     ```java
     session.update("..."，user对象);
     session.commit();
     ```

* 存入主键信息到对象

  ```xml
  <selectKey keyProperty="返回的主键存入输入参数的属性"
             order="AFTER|BEFORE"在之前之后|执行
             resultType="返回的主键类型">
     <!--mysql查询上一个自增的主键--> 
      select LAST_INSERT_ID()
  </selectKey
  ```

  ```java
  session.insert("xx",对象);
  对象.get属性();//取得主键
  ```

---

### 四、Mybatis的dao

<font size=4>原生dao方法（接口+实现类）；动态代理方法（只提供接口，由Mybatis创建实现类）</font>

<font color="red">session线程不安全，最好在方法体内生成</font>

<font color="green">Junit注解 @Before用在注解上，该方法会在@Test方法之前执行</font>

* Mybatis动态代理dao：只提供接口，实现类有Mybatis实现

  <font color="red">原则</font>

  > 映射文件与接口必须在同一目录下，且同名
  >
  > ---
  >
  > namespace=“接口path”
  >
  > id=“接口方法名”
  >
  > parameterType=“接口方法的参数类型”
  >
  > resultType=“接口方法的返回类型”，返回结果集为List时Mybatis生成实现类自动调用selectList方法
  >
  > ---

  

  核心配置文件

  ```xml
  <mappers>
  	<mapper class="接口path"/>
  </mappers>
  ```

  生成实现类

  ```java
  接口 imp = session.getMapper(接口.class);
  imp.方法(参数);
  session.commit();
  ```

---

### 五、SqlMapConfig.xml的部分标签

* ```properties```：加载properties文件

  新建```db.properties```，用来设置数据库参数,键值对形式

  ```xml
  jdbc.driver=com.mysql.jdbc.Driver
  jdbc.url=jdbc:mysql://localhost:3306/SSH
  jdbc.username=SSH
  jdbc.password=SSH
  ```

  引入外部properties文件，可以使用EL表达式取值

  ```xml
  SqlMapConfig.xml
  <configuration>
      <properties resource="外部文件路径"/>
      .....
      <!--设置连接池-->
      <property name="url" value="${jdbc.url}"/>
      ....
  </configuration>
  ```

* ```typeAliases```：别名

  映射文件中的parameterType=“int” ，int被定义为```java.lang.Integer```的别名

  ```xml
  <typeAliases>
      <!--定义单个pojo的别名-->
  	<typeAlias type="类path" alias="别名"/>
      <!--开启包扫描，批量定义别名-->
      <package name="包path"/>
      <!--别名=类名 不区分大小写,最后遵循规范-->
  </typeAliases>
  ```

* ```mapper```：映射器

  ```xml
  <mappers>
      <mapper resource="映射文件路径"/>
      <mapper class="需要动态代理的接口path"/>
      <!--批量引入需要代理的接口-->
      <package name="包path"/>
  </mappers>
  ```

---

### 六、输入、输出映射

* 输入映射（parameterType）

  > 基本类型，String，pojo类型，vo类型

* 输出映射（resultType）

  > 基本类型，pojo类型，List类型，
  >
  > map不常用
  >
  > 只有返回结果为一条记录时，返回值才可以是基本类型

### 七、动态sql

<font size=4>实现拼接sql的功能，传统JDBC只能通过拼接字符串来实现</font>

***Mybatis拼接查询***，条件查询```select ..from .. where .. and .. or ..```

```xml
<select id parameterType="xxx.User"
        resultType="xxx.User">
    select * from Users
  <!--如果有if能够匹配就自动拼接where ，并省略第一个and-->
    <where>
        <!--传入设置了uname属性的user-->
        <if test="uname != null and uname != ''">
        	and uname like "%${uname}%"
        </if>
    </where>
</select>
```

```select * from … where uid in (1,2,5)的拼接方法```

```xml
在传入参数的类中声明并实例化lsit集合uids，用来存储(1,2,5)
<select id parameterType resultType>
	select * from Users
    <where>
        <if test="uids != null">
<!-- in ( 1,2,5 )-->
            <foreach collection="要遍历的集合u ids"
                     item="每次遍历的对象 uid"
                     open="in (" 遍历开始时的拼接符
                     separator="," 分隔符
                     close=")" 遍历结束时的拼接符>
                #{uid}	取出遍历对象
            </foreach>
        </if>
    </where>
</select>
```

封装拼接条件，实现sql条件的复用

```xml
<sql id="">
	<where>....</where>
</sql>
<!--调用该条件-->
<include refid=""/>
```



### 八、关联查询

* 一对一：一个订单记录对应一个用户记录

  查询所有订单及其对应的用户

  1. 自动映射：新建pojo，包含两表所有字段属性

     ```该方法只用写一个配置文件，且不用指定属性与字段的映射关系；属性名必须与查询后的字段名一致```

     ```sql
     select * from Orders o,Users u where u.uid=o.u_o
     ```

  2. 手动映射：在订单表中新设用户对象作为属性，并手动配置映射关系

     ```xml
     <resultMap id='map' 
                type='返回的封装对象类型/集合的泛型'>
         <id column="主键字段" property="属性"/>
         <result column="字段" property="属性"/>
         ....
         <!--封装对象属性的属性-->
         <association property="对象属性"
                      javaType="对象属性类型">
         	<id column="主键名"
                 property="属性"/>
             <result ..../>
         </association>      
     </resultMap>
     
     <select id="" resultMap='map'>
     	select * from Orders o,Users u where u.uid=o.u_o
     </select>
     ```

* 一对多：一个用户记录对应多个订单记录

  查询所有用户及其对应的所有订单

  在用户表中新设List集合，用来存放订单，映射只能手动配置

  ```xml
  <resultMap id='map' type="返回类型/泛型">
  	<id .../>
      <result .../>
      ...
      <!--封装集合对象中的对象的属性-->
      <collection property="集合名"
                  ofType="集合的泛型">
      	<id ../>
          <result .../>
          ...
      </collection>
  </resultMap>
  
  <select id="" resultMap="map">
      select * from Users u,Orders o where u.uid=o.u_o
  </select>
  ```

### 九、整合Spring

* 导包：Mybatis，spring，mysql，整合包

* 创建config/applicationContext.xml、SqlMapConfig.xml

  ```xml
  applicationContext.xml
  <beans>
  	<!--配置连接池-->
      <bean id="dataSource" class=>.</bean>
      
      <!--配置sqlsessionfactory-->
      <bean id="sqlSessionFactory"
         class="...SqlSessionFactoryBean">
      	<property name="dataSource" ref/>
          <property name="configLocation"
             value="classpath:SqlMapConfig.xml"/>       </bean>
      
      <!--配置实现类-->
      1. 传统dao实现类,继承SqlSessionDaoSupport
      <bean id="daoImp" class=>
          <property name="sqlSessionFactory"
                    ref=/>
      </bean>
  
  </beans>
  ```

* 使用动态代理，由Mybatis生成实现类，SqlMapConfig中不需要再写映射文件

  ```xml
      2.动态代理方法,定义一个实现类
      <bean id="daoImp" 
            class="...MapperFactoryBean">
      	<property name="mapperInterface"
                    value="接口path"/>
          <property name="sqlSessionFactory"
                    ref=""/>
      </bean>
      3.动态代理方法，批量定义实现类,id为接口名，首字母小写
      <bean class="...MapperScannerConfigurer">
      	<property name="basePackage"
                    value="包路径，包路径,.."/>
          <property name="sqlSessionFactory"
                    ref=""/>
      </bean>
  ```

### 十、mybatis逆向工程

<font size=4>根据数据库表结构自动生成po，生成单表的动态代理：映射文件和接口</font>

<font size=4>逆向工程通过追加写方式创建映射，接口，po；</font>

使用流程：

```
1. 新建工程
2. 导包：Mybatis核心包，数据库驱动包，Mybatis-generator核心包
3. 将generator.xml放到根目录，并修改参数
4. 修改程序，并执行
```

生成的pojo中有xxxExample类

使用方法：

```java
接口中的方法：基本的方法，使用生成的xxxExample进行
selectByPrimaryKey(主键);//基本查询，返回对象
//多条件查询
xxExample example = new xxxExample();//创建example
//Criteria是Example的内部类，封装了查询条件
Criteria c = example.createCriteria();//创建条件对象
c.andUsernameLike("%xx%");//添加条件，like要写%
c.andSexEqualTo("x");//添加条件
selectByExample(example);//返回List集合
```



### 十一、Mybatis使用中遇到的问题

* PageHelper不支持关联查询分页

  解决方案：

  ```shell
  在reualtMap的 Collection中手动指定嵌套查询
  <resultMap id="userAndRole" type="com.jissi.basic.repository.entity.User">
          <id property="uid" column="uid"></id>
          <result property="username" column="username"></result>
          <result property="nickname" column="nickname"></result>
          <result property="avatar" column="avatar"></result>
          
          <collection property="roles" ofType="com.jissi.basic.repository.entity.Role"
                      select="subQueryRole" column="{urUid = uid}"> 
                      <!--指定嵌套查询，并向查询传惨urUuid,其值为主查询的值-->
              <id property="id" column="id"></id>
              <result property="roleName" column="role_ame"></result>
          </collection>
  </resultMap>
  
  主查询只负责查询User信息，从查询负责根据uid查询Role信息
  Mapper接口只需指定主查询
  ```

  

