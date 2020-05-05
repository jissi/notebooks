<h1>SSM + shiro 配置文件</h1>
[TOC]



#### xml方式

* web xml

  ```xml
  <!--shiro过滤器-->
  <filter>
      <filter-name>shiroFilter</filter-name>
      <!--spring创建对象，交给servlet容器管理-->
      <filter-class>org.springframework.web.filter.DelegatingFilterProxy</filter-class>
      <init-param>
          <!--生命周期由servlet控制-->
          <param-name>targetFilterLifecycle</param-name>
          <param-value>true</param-value>
      </init-param>
  </filter>
  <filter-mapping>
      <filter-name>shiroFilter</filter-name>
      <url-pattern>/*</url-pattern>
  </filter-mapping>
  
  <!--首先加载配置文件-->
  <context-param>
      <param-name>contextConfigLocation</param-name>
      <param-value>classpath:applicationContext.xml,classpath:shiro.xml</param-value>
  </context-param>
  <!--Spring配置加载-->
  <listener>
      <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
  </listener>
  <!--post乱码处理-->
  <filter>
      <filter-name>postEncodingFilter</filter-name>
      <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
      <init-param>
          <param-name>encoding</param-name>
          <param-value>UTF-8</param-value>
      </init-param>
  </filter>
  <filter-mapping>
      <filter-name>postEncodingFilter</filter-name>
      <url-pattern>/*</url-pattern>
  </filter-mapping>
  
  <!--配置dispatcherServlet-->
  <servlet>
      <servlet-name>dispatcherServlet</servlet-name>
      <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
      <init-param>
          <param-name>contextConfigLocation</param-name>
          <param-value>classpath:springMvc.xml</param-value>
      </init-param>
      <load-on-startup>1</load-on-startup>
  </servlet>
  <servlet-mapping>
      <servlet-name>dispatcherServlet</servlet-name>
      <url-pattern>/</url-pattern>
  </servlet-mapping>
  ```



* spring配置文件

  ```xml
  <!--组件扫描-->
  <context:component-scan base-package="com.test"/>
  <!--加载外部配置-->
  <context:property-placeholder location="classpath:config.properties"/>
  <!--dao配置-->
  <!--数据源-->
  <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
      <property name="driverClassName" value="${jdbc.driver}"/>
      <property name="url" value="${jdbc.url}"/>
      <property name="username" value="${jdbc.username}"/>
      <property name="password" value="${jdbc.password}"/>
  </bean>
  <!--声明式事务-->
  <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
      <property name="dataSource" ref="dataSource"/>
  </bean>
  <tx:annotation-driven transaction-manager="transactionManager"/>
  <!--mybatis-->
  <bean id="sessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
      <property name="dataSource" ref="dataSource"/>
      <property name="mapperLocations" value="classpath:mapper/*.xml"/>
  </bean>
  <!--mapper扫描-->
  <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
      <property name="sqlSessionFactoryBeanName" value="sessionFactory"/>
      <property name="basePackage" value="com.test.dao.mapper"/>
  </bean>
  ```

  

* spring mvc配置文件

  ```xml
  <context:property-placeholder location="classpath:config.properties"/>
  <!--controller扫描-->
<context:component-scan base-package="com.test.controller"/>
  <!--开启aop 对类代理-->
  <aop:config proxy-target-class="true"/>
  <!--shiro注解支持-->
  <bean class="org.apache.shiro.spring.security.interceptor.AuthorizationAttributeSourceAdvisor">
      <property name="securityManager" ref="securityManager"/>
  </bean>
  
  <!--自动配置 HandlerMapping 和 HandlerAdapter-->
  <mvc:annotation-driven/>
  <!--视图解析器-->
  <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
      <property name="prefix" value="${mvc.view.prefix}"/>
      <property name="suffix" value="${mvc.view.suffix}"/>
  </bean>
  <!--拦截器-->
  <!--    <mvc:interceptors>
          <mvc:interceptor>
              <mvc:mapping path="/**"/>
              <bean></bean>
          </mvc:interceptor>
          <bean></bean>
      </mvc:interceptors>-->
  ```
  



* shiro.xml

  ```xml
  <!--凭证匹配器-->
  <bean id="credentialsMatcher" class="org.apache.shiro.authc.credential.HashedCredentialsMatcher">
      <property name="hashAlgorithmName" value="MD5"/>
      <property name="hashIterations" value="2"/>
  </bean>
  <!--自定义realm-->
  <bean id="druidRealm" class="com.test.shiro.DruidRealm">
      <property name="credentialsMatcher" ref="credentialsMatcher"/>
  </bean>
  <!--缓存管理器-->
  <bean id="cacheManager" class="org.apache.shiro.cache.ehcache.EhCacheManager">
      <property name="cacheManagerConfigFile" value="classpath:ehcache.xml"/>
  </bean>
  <!--session管理器 失效时间ms，删除失效的session-->
  <bean  id="sessionManager" class="org.apache.shiro.web.session.mgt.DefaultWebSessionManager">
      <property name="globalSessionTimeout" value="300000"/><!--失效时间 ms-->
      <property name="deleteInvalidSessions" value="true"/>
  </bean>
  <!--记住我-->
  <bean id="rememberMeManager" class="org.apache.shiro.web.mgt.CookieRememberMeManager">
      <property name="cookie" ref="cookie"/>
  </bean>
  <!--cookie  用户信息必须序列化-->
  <bean id="cookie" class="org.apache.shiro.web.servlet.SimpleCookie">
      <property name="maxAge" value="604800"/><!--保存时间 7天-->
      <property name="name" value="rememberMe"/><!--名称-->
  </bean>
  <!--security manager-->
  <bean id="securityManager" class="org.apache.shiro.web.mgt.DefaultWebSecurityManager">
      <property name="realm" ref="druidRealm"/>
      <property name="cacheManager" ref="cacheManager"/>
      <property name="sessionManager" ref="sessionManager"/>
      <property name="rememberMeManager" ref="rememberMeManager"/>
  </bean>
  
  <bean id="lifecycleBeanPostProcessor" class="org.apache.shiro.spring.LifecycleBeanPostProcessor"/>
  
  <!--id必须为 logout 配置shiro logout过滤器-->
  <bean id="logout" class="org.apache.shiro.web.filter.authc.LogoutFilter">
      <!--默认注销后跳转 /-->
      <property name="redirectUrl" value="/toLogin"/>
  </bean>
  <!--配置 FormAuthentication过滤器 可以在form使用其他名称-->
  <bean id="authc" class="org.apache.shiro.web.filter.authc.FormAuthenticationFilter">
      <property name="usernameParam" value="name"/>
      <property name="passwordParam" value="password"/>
      <property name="rememberMeParam" value="rememberMe"/>
  </bean>
  
  
  <bean id="shiroFilter" class="org.apache.shiro.spring.web.ShiroFilterFactoryBean">
      <!--指定security manager-->
      <property name="securityManager" ref="securityManager"/>
      <!--认证失败后跳转路径 默认/login.jsp-->
      <property name="loginUrl" value="/login"/>
      <!--认证成功后跳转，默认跳转上一条url-->
      <property name="successUrl" value="/success"/>
      <!--权限不够时跳转路径-->
      <property name="unauthorizedUrl" value="/refuse"/>
      <!--shiro 过滤器链-->
      <property name="filterChainDefinitions">
          <!--url=拦截器  anon:匿名访问 authc:执行认证 -->
          <value>
              /toLogin=anon
              <!--登录时走 authc过滤器 FormAuthenticationFilter-->
              /login=authc
              <!--shiro 提供的登出-->
              /logout=logout
              /js/**=anon
              /css/**=anon
              <!--该url 可以使用记住我直接访问-->
              /user/users=user
              /**=authc
          </value>
      </property>
  </bean>
  ```

  



#### JavaConfig方式

* WebApplicationInitalizer 替代 web.xml   位于根目录下：com.test.WebApplicationInitalizer

```java
public class WebApplicationInitalizer extends AbstractAnnotationConfigDispatcherServletInitializer {
    //加载spring配置类
    @Override
    protected Class<?>[] getRootConfigClasses() {
        return new Class[]{RootConfig.class};
    }

    //加载springmvc配置类
    @Override
    protected Class<?>[] getServletConfigClasses() {
        return new Class[]{WebMvcConfig.class};
    }
    //拦截所有请求
    @Override
    protected String[] getServletMappings() {
        return new String[]{"/"};
    }
}
```

* WebMVCConfig 替代 springMvc.xml

```java
@Configuration
@ComponentScan(basePackages = {"com.test.controller"})
public class WebMvcConfig extends WebMvcConfigurerAdapter {
	//请求转发
    @Override
    public void addViewControllers(ViewControllerRegistry registry) {
        registry.addViewController("/").setViewName("index");
    }
    // 配置视图解析器
    @Bean
    public ViewResolver viewResolver(){
        InternalResourceViewResolver resolver = new InternalResourceViewResolver();
        resolver.setPrefix("/templates/");
        resolver.setSuffix(".jsp");
        return resolver;
    }
}
```

* RootConfig 替代 applicationContext.xml

```java
@Configuration
@EnableTransactionManagement
@ComponentScan(basePackages = {"com.test"})
public class RootConfig {
    //为了使用@Vlue注入properties中的属性
    @Bean
    public PropertySourcesPlaceholderConfigurer placeholderConfigurer(){
        return new PropertySourcesPlaceholderConfigurer();
    }
}
```



---

#### POM文件

```xml
<packaging>war</packaging>
<dependencies>
    <!--servlet-->
    <dependency>
        <groupId>javax.servlet</groupId>
        <artifactId>javax.servlet-api</artifactId>
        <version>3.1.0</version>
    </dependency>
    <dependency>
        <groupId>javax.servlet.jsp</groupId>
        <artifactId>javax.servlet.jsp-api</artifactId>
        <version>2.3.2-b02</version>
    </dependency>
    <dependency>
        <groupId>javax.servlet</groupId>
        <artifactId>jstl</artifactId>
        <version>1.2</version>
    </dependency>
    <!--spring-->
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-webmvc</artifactId>
        <version>5.2.0.RELEASE</version>
    </dependency>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-context-support</artifactId>
        <version>5.2.0.RELEASE</version>
    </dependency>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-aop</artifactId>
        <version>5.2.0.RELEASE</version>
    </dependency>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-tx</artifactId>
        <version>5.2.0.RELEASE</version>
    </dependency>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-jdbc</artifactId>
        <version>5.2.0.RELEASE</version>
    </dependency>
    <!--dao-->
    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
        <version>5.1.6</version>
    </dependency>
    <dependency>
        <groupId>com.alibaba</groupId>
        <artifactId>druid</artifactId>
        <version>1.1.10</version>
    </dependency>
    <dependency>
        <groupId>org.mybatis</groupId>
        <artifactId>mybatis-spring</artifactId>
        <version>1.3.1</version>
    </dependency>
    <dependency>
        <groupId>org.mybatis</groupId>
        <artifactId>mybatis</artifactId>
        <version>3.4.5</version>
    </dependency>
    <!--日志-->
    <dependency>
        <groupId>log4j</groupId>
        <artifactId>log4j</artifactId>
        <version>1.2.17</version>
    </dependency>
    <dependency>
        <groupId>org.slf4j</groupId>
        <artifactId>slf4j-log4j12</artifactId>
        <version>1.7.12</version>
    </dependency>
    <!--shiro-->
    <dependency>
        <groupId>org.apache.shiro</groupId>
        <artifactId>shiro-core</artifactId>
        <version>1.2.4</version>
    </dependency>
    <dependency>
        <groupId>org.apache.shiro</groupId>
        <artifactId>shiro-web</artifactId>
        <version>1.2.4</version>
    </dependency>
    <dependency>
        <groupId>org.apache.shiro</groupId>
        <artifactId>shiro-spring</artifactId>
        <version>1.2.4</version>
    </dependency>
    <!--jackson-->
    <dependency>
        <groupId>com.fasterxml.jackson.core</groupId>
        <artifactId>jackson-core</artifactId>
        <version>2.10.0</version>
    </dependency>
    <dependency>
        <groupId>com.fasterxml.jackson.core</groupId>
        <artifactId>jackson-databind</artifactId>
        <version>2.10.0</version>
    </dependency>
    <dependency>
        <groupId>com.fasterxml.jackson.core</groupId>
        <artifactId>jackson-annotations</artifactId>
        <version>2.10.0</version>
    </dependency>
</dependencies>
```

