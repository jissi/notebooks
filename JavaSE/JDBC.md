## Java DataBase Connectivity

1. **安装jdbc Driver for Mysql**

   mysql官网下载jdbc驱动，rpm包解压后的jar文件移动至jkd/jre/lib/ext/目录下

2. **加载数据库驱动**

   Mysql数据库驱动封装在Driver类，包名为com.mysql.jdbc.Driver  ;新版本使用com.mysql.cj.jdbc.Driver

   ```java
   try{
       Class.forName("com.mysql.cj.jdbc.Driver");//Class类实例方法，返回一个与类有关的对象
   }catch(Exception e){}
   ```

3. **连接数据库**

   java.sql包中的DriverManager类有两个创建连接对象（Connection对象）的static方法

   ```java
   Connection getConnection("jdbc:mysql://127.0.0.1:3306/数据库?参数设置","用户","密码")
   Connection getConnection("jdbc:mysql://127.0.0.1:3306/数据库?user=用户&password=密码&参数设置")
   ```

   <font color="red">数据库中有中文时要加参数  characterEncoding=utf-8</font>

   eg.连接本机数据库

   ```java
   try{
       Connection con = DriverManager.getConnection("jdbc:mysql://127.0.0.1:3306/dbforjissi?user=jissi&password=jissi&useSSL=true")
   }
   ```

   

4. **查询操作**

   * 创建sql语句对象statement

     ```java
     try{
         Statement sql = con.createStatement();//连接对象调用createStatement方法返回语句对象
     }catch(SQLException e){}
     ```
     
* 查询
  
  <font color="red">查询结果存放在ResultSet对象中</font>
  
  ```java
     ResultSet rs = sql.executeQuery("select * from students")
         //sql语句对象调用exeuteQuery("sql")方法返回ResultSet对象
  ```
  
  ResultSet对象由按字段（属性，列）组织的数据行构成，索引从1开始
  
  ```java
     ResultSet对象的方法
     boolean next()//游标下移，是否还有数据
     byte getByte(索引或"列名")//返回一个字节数据
     Date getDate(索引或"列名")//返回一个Date型数据
     double\float\int\long
     String getString(索引或"列名")//返回数据的字符串表示，可以返回任意类型数据
  ```
  

​    

* <font color="red">查询表的列数 ResultSetMetaData对象   </font>
  
  ```java
     ResultSetMetaData metaData = rs.getMetaData()//创建ResultSetMetaData对象
     metaData.getColumnCount()// int型，返回列数
     metaDate.getColumnName(i)//String型，返回第i列的列名
  ```
  
  
  

  
5. **关闭连接**

   Connection对象被关闭，ResultSet对象内的查询结果将立即消失

   ```java
   con.close()//关闭连接对象
   ```

6. **控制游标**

   ```java
   Statement sql = con.createStatement(int type,int concurrency);
   /*type是游标滚动类型：
    * ResultSet.TYPE_FORWARD_ONLY //结果集的游标只能向下滚动
    * ResultSet.TYPE_SCROLL_INSENSITIVE //游标上下移动，数据库变化时，结果集不变
    * ResultSet.TYPE_SCROLL_SENSITIVE  //游标上下移动，数据库变化时，结果集同步更新
    */
   /*concurrency决定是否用结果集更新数据库
    *ResultSet.CONCUR_READ_ONEY //不能用结果集更新数据库
    *ResultSet.CONCUR_UPDATABLE //能用结果集更新数据库
   */
   ```

   滚动查询的结果集方法

   ```java
   boolean previous()  //游标上移，到头时返回false
   void beforeFirst()   //游标移动到第一行之前，回到起始位置
   void afterLast()    //移动到最后一行之后
   boolean isBeforeFirst()/isAfterLast() //是否在第一行之前/最后一行之后
   void first()/last() //移到第一行/最后一行
   boolean isFirst()/isLast()  //是否在第一行/最后一行
   int getRow()        //返回当前行号，没有行返回0
   boolean absolute(int row) //移动到指定行，负数就是倒数第几行，移到afterFirst或afterLast处时返回flase
   ```

   

7. **更新，删除与添加**

   ```java
   ResultSet reult = statement.executeUpdate("update student set age=10 where name='888'")
   ```

   返回结果集后没有立即输出，而是执行了更新语句，则需要重新返回结果集才能输出

8. **预处理语句**

   PreparedStatement对象：将SQL语句预编译成数据库底层内部命令，减小数据库负担

   ```java
   PreparedStatement sql = con.preparedStatement(String sql)
   ResultSet result = sql.executeQuery()//执行查询
   boolean result = sql.execute()// true means Query,flase means Update
   int result = sql.executeUpdate()//更新表
   ```

   <font color="green">PreparedStatement对象使用通配符 “？”</font>

   <font color="green">必须在预处理对象执行之前定义通配符“?”代表的值</font>

   ```java
   void setDate(int parameterIndex,Date date)//设置第几个通配符代表什么
   void setInt(...)\setDouble(...) 
   ```

   

   ```java
   String str = "select name from students where age > ? and age < ?"
       //两个通配符“？”
   PreparedStatement sql = con.preparedStatement(str)//构造预处理对象
   sql.setInt(1,18)//设置第一个"?"代表18
   sql.setInt(2,25)//设置第二个"?"代表25
   ResultSet result = sql.executeQuery()
   ```

   

9. **事务处理**

   几条SQL语句要么都执行，要么都不执行

   * 关闭自动提交

     创建的连接对象con默认自动提交，在创建Statement对象前关闭自动提交

     ```java
     con.setAutoCommit(false)//关闭自动提交
     ```

   <font color="red">先关闭自动提交，再创建Statement对象</font>

   * 使用commit()提交事务

     关闭自动提交后，Statement/PreparedStatement对象提交的SQL语句不会立即生效，就可以提交多条SQL语句。连接对象.commit()提交事务

     ```java
     con.commit()//提交事务，所有SQL语句生效
     ```

   * 回滚处理事务失败

     事务中有任何一条SQL语句出错，就会触发SQLException异常。

     ```java
     con.rollback()//撤销con.commit()
     ```

   ---
   
   ### 数据源（javax.sql.DataSource）<font color="red">（失败了）</font>
   
   <font size=4>javax.sql.DataSource接口负责建立与数据库的连接，访问数据库时不必编写连接代码，直接从数据源获得连接</font>

* 数据源与数据库连接池

  数据源建立了多个数据库连接，并将其存放在数据库连接池（Connection Pool），java程序访问数据库时，从连接池取出空闲的连接，用完后再放回连接池。提高了访问数据库的效率。

* 数据源和JNDI资源

  DataSource对象由Servlet容器提供，java程序不用自己创建DataSource对象。

  JNDI（Java Naming and Directory Interface）技术：将对象和名字绑定，对象工厂负责生产对象，这些对象与唯一的名字绑定，外部程序通过名字来访问对象的引用。

  <font color="green">Javax.naming.Context接口</font>

  ````
  bind(String name,Object onject) //将对象与名字绑定
  lookup(String name)		//返回与名字绑定的对象的引用
  ````

  在web应用的根目录新建 META-INF/context.xml

  ```xml
  <Context reload="true">
      <Resource name="绑定的名字" 
                auth="Container" 管理数据源的管理器：Container|Application 由容器/web应用 创建和管理Sesource
                type="javax.sql.DataSource" 指定Resource所属的java类
                maxActive="100" 连接池中处于活动状态的连接的最大数目，0表示不受限
                maxIdle="30" 连接池处于空闲的连接的最大数目，0表示不受限
                maxWait="10000" 空闲最大时长，超时抛出异常，-1表示无限等待
                username="数据库用户"
                password="密码"
                driverClassName="com.mysql.cj.jdbc.Driver"驱动其名
                url="jdbc:mysql://localhost:3306/dbforjissi?autoReconnect=true"
  </Context>
  ```

  在web.xml中加入<resource-ref>

  ```xml
  <web-app>
      <resource-ref>
          <description>说明</description>
          <res-ref-name>绑定的名字</res-ref-name>
          <res-ref-type>javax.sql.DataSource</res-ref-type>
          <res-auth>Container</res-auth>
      </resource-ref>
  </web-app>
  ```

  访问数据库

  ```java
  Context context = new InitialContext();
  DataSource ds = (DataSource)context.lookup("绑定的名字");//取得DataSource对象的引用
  Connection con = ds.getConnection();//连接数据库
  con.close();//Connection对象返回连接池，进入空闲状态
  
  ```

### 分页显示

```java
int 每页显示的记录数 = 3
int 当前页数 = 0
int 总页数 = 0
int 取值起始索引 = 0
//计算总页数
    select count(*) from books;
当前页数 = (总页数%每页记录==0)?(总页数/每页记录数):(总页数/每页记录数+1)
//当前页码
当前页码 = request.getParameter("页码")
确保  0<=当前页码<=总页数
//计算起始索引
索引 = (当前页码  -1)*每页记录
//取出指定范围的数据（limit index，count）
select * from books limit 索引,每页记录;
//显示所有页码（除了当前页码都可以跳转）
for(int i=1;i<=总页数;i++){
    if(i==当前页码) 输出页码
    else{
        <a href="mainpage.jsp?页码=i">i</a>
    }
}
//取出指定的数据并输出
```

