<h1>SpringSecurity</h1>
非功能性需求，使用过滤器和拦截器也能实现。

常见框架：SpringSecurity shiro

主要功能：认证(Authentication)、授权(Authorization)

导入依赖，编写配置类继承 WebSecurityConfigurerAdapter ,控制请求访问权限

403:权限不足



[TOC]



#### 简单使用

* 导入依赖 spring-boot-starter-security
* 自定义配置类 @EnableWebSecurity  继承 WebSecurityConfigurerAdapter

```java
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {
//重写请求授权规则
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http.authorizeRequests()
                //允许首页访问
                .antMatchers("/").permitAll()
                //所有 level1下的请求需要角色
                .antMatchers("/level1/**","/userlogin").hasRole("Vip1")
                .antMatchers("/level2/*").hasRole("Vip2")
                .antMatchers("/level3/**").hasRole("Vip3");
        //自动配置登录功能：无法鉴定权时限自动跳转登录页面
        //发送/login请求来到登录页
        //登录错误重定向到 /login?error
        //.usernameParameter()设置表单name ,详见HttpSecurity源码
        http.formLogin();
        // （默认post直接注销、get会要求确认）发送/logout清空session 重定向/login?success
        http.logout().logoutSuccessUrl("/");
        //记住我
        http.http.rememberMe();
    }
 //身份认证
    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
        //内存用户,密码需要经过加密
        auth.inMemoryAuthentication()
                .passwordEncoder(new BCryptPasswordEncoder())
                .withUser("abi").password(new BCryptPasswordEncoder().encode("123")).roles("Vip1","Vip2")
                .and()
                .withUser("aci").password(new BCryptPasswordEncoder().encode("123")).roles("Vip2","Vip3")
                .and()
                .withUser("test").password(new BCryptPasswordEncoder().encode("123")).roles("Vip1","Vip3");
    }
}
```



#### Thymeleaf支持

* 导入依赖

  ```xml
  <!--thymeleaf security支持-->
  <dependency>
      <groupId>org.thymeleaf.extras</groupId>
      <artifactId>thymeleaf-extras-springsecurity5</artifactId>
      <version>3.0.3.RELEASE</version>
  </dependency>
  ```

* 使用 sec:

  ```html
  <div sec:authorize="isAuthenticated()">已登录</div>
  <div sec:authorize="hasRole('admin')">管理员</div>
  <div sec:authentication="name">用户名</div>
  <div sec:authentication="principal.authorities">角色</div>
  ```

  