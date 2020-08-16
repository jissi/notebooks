<h2>Maven</h2>
<font size=4>用于构建和管理java项目的工具</font>

---

[TOC]

---

### 一、相同的项目结构

> pom.xml					用于维护当前项目使用的jar包
>
> src/main/java			存放java代码
>
> src/test/java				存放测试代码

### 二、统一维护jar包

> 将所有的jar包放在仓库，适应是提供jar包名和版本号，实现jar包共享

### 三、安装配置Maven

> 解压安装包到 /usr/local/maven
>
> 添加环境变量：

```shell
vim /etc/profile
export MAVEN_HOME=/var/local/apache-maven-3.5.2
export MAVEN_HOME
export PATH=$PATH:$MAVEN_HOME/bin
source /etc/profile
```

> 检验：mvn -v

### 四、Maven仓库

1. 仓库用于存放项目需要的jar包，多个项目共享一个仓库内的jar包

2. **仓库位置**

   ```shell
   maven_home/conf/settings.xml 的52行指定了仓库位置
   ${user.home}/.m2/repository
   ```

   ```xml
   修改仓库位置
   <localRepository>.../maven/repository</localRepository>
   ```

3. **默认下载源**

   默认从官方服务器下载jar包

4. **使用阿里源**

   ```xml
   vim /usr/local/src/...../conf/settings.xml
   第160-165行
   <mirror>
      <id>alimaven</id>
      <mirrorOf>central</mirrorOf>
      <name>aliyun maven</name>    <url>http://maven.aliyun.com/nexus/content/repositories/central/</url>
   </mirror>
   ```

### 五、创建Maven项目

1. 使用Maven命令来创建Maven项目

   ```shell
   切换到要放项目的目录 ../data/project/
   mvn archetype:generate -DgroupId=com.how2java -DartifactId=j2se -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
   ```

   ```shell
   #参数说明
   archetype:generate 表示创建个项目
   -DgroupId=项目包名: com.how2java
   -DartifactId=项目名称: j2se
   -DarchetypeArtifactId=项目类型: maven-archetype-quickstart
   -DinteractiveMode=false 表示前面参数都给了，就不用一个一个地输入了
   ```

   执行完命令自动创建Maven项目结构

2. 运行 package命令（编译，测试，打包，生成jar包）

   ```shell
   cd 项目根目录
   mvn package
   #生成 j2ee-....jar包
   ```

3. 执行jar

   ```shell
   java -cp target/j2se-1.0-SNAPSHOT.jar com.how2java.App
   ```


### 六、使用Idea配置Maven

1. 配置Maven

   ```
   idea settings：
   file -> settings -> build,Execute... -> build tools -> maven
   修改Maven home ，user setting file,local repository
   ```

2. 新建Maven项目

   * 创建项目

     ```
     file -> new -> project -> 左边选Maven
     勾选 create from archetype
     选择 org.apache.maven.archetypes:maven-archetype-quickstart
     next
     项目参数：
     	groupId：项目包名
     	artifactId：项目名
     	Version：版本
     next
     检查参数是否有误
     next 确认项目
     弹出需要import(右下角),点击import
     ```

   * 增加jar

     ```java
     主程序中直接使用包
     修改pom.xml
     ```
     
     ```xml
     <dependencies>
         <dependency>
             <groupId>mysql</groupId>
             <artifactId>mysql-connector-java</artifactId>
             <version>5.1.20</version>
         </dependency>
     </dependencies>
     点击import channges
     ```

### 七、idea配置Maven web项目

* Idea 2018存在bug，需要如下配置

  ```
  file - settings - build,execution... - build tools - maven - importing
   - 取消 store generated pro files externally
  ```

* 新建webapp

  ```
  file - new - project - Maven
   - 勾选 create from archetype
   - 选择 org.apache.maven.archetypes:maven-archetype-webapp
  ```

  手动创建源代码目录

  ```
  右键main目录 - 新建java目录 - 右键java目录 - make directory as - 
   - source root
  ```

  创建servlet

  ```
  右键java目录 - new - servlet
  ```

  ```xml
  导入servlet依赖包
  pom.xml
  <dependencies>
      <dependency>
         <groupId>javax.servlet</groupId>
          <artifactId>javax.servlet.api</artifactId>
          <version>3.1.0</version>
      </dependency>
  </dependencies>
  ```

### 八、父子聚合

* 父子-聚合项目

  > 一个父项目，多个子项目
  >
  > 在业务逻辑上，子项目都归纳在父项目下
  >
  > 重复使用的jar包都放在父项目下进行依赖

* 创建父子聚合项目

  ```xml
  1. 先新建一个Maven项目
  2. 修改pom.xml
  <packaging>pom</packaging> 将jar改为pom
  添加hutool 和 junit包的依赖
    <dependencies>
      <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
        <version>4.11</version>
        <scope>test</scope>
      </dependency>
      <dependency>
        <groupId>cn.hutool</groupId>
        <artifactId>hutool-all</artifactId>
        <version>4.3.1</version>
      </dependency>
    </dependencies>
  3. 创建子项目（maven module）
  右键项目 - new - module
  相同的方法创建Maven项目，子项目位于父项目目录下
  ```




### 九、使用笔记

#### 指定编译版本

```xml
<properties>
    <maven.compiler.source>1.8</maven.compiler.source>
    <maven.compiler.target>1.8</maven.compiler.target>
</properties>
```

