<h1>SpringSecurity</h1>
非功能性需求，使用过滤器和拦截器也能实现。

常见框架：SpringSecurity shiro

主要功能：认证(Authentication)、授权(Authorization)

导入依赖，编写配置类继承 WebSecurityConfigurerAdapter ,控制请求访问权限

403:权限不足



[TOC]

### 一、认证

* 导入依赖 spring-boot-starter-security
* 自定义配置类 @EnableWebSecurity  继承 WebSecurityConfigurerAdapter

```java
@Configuration
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
        http.formLogin()
            .successHandler((request,response,authentication)->{//认证成功处理
                    response.setContentType("application/json;charset=utf-8");
                    PrintWriter writer = response.getWriter();
                    writer.write(new ObjectMapper().writeValueAsString(Results.loginSuccess()));
                    writer.flush();
                    writer.close();
                })  //同理，认证失败处理
            .permitAll();
        // （默认post直接注销、get会要求确认）发送/logout清空session 重定向/login?success
        http.logout().logoutSuccessUrl("/");
        //记住我
        http.rememberMe().tokenValiditySeconds(100).key("rememberMe");
        
        //鉴权异常处理
        http.exceptionHandling()
                .accessDeniedHandler(((request, response, accessDeniedException) -> {
                    .....
                }));
    }
    //身份认证
    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
        //auth.userDetailsService(userService).passwordEncoder(passwordEncoder());
        //内存用户,密码需要经过加密
        auth.inMemoryAuthentication()
                .passwordEncoder(new BCryptPasswordEncoder())
                .withUser("abi").password(new BCryptPasswordEncoder().encode("123")).roles("Vip1","Vip2")
                .and()
                .withUser("aci").password(new BCryptPasswordEncoder().encode("123")).roles("Vip2","Vip3")
                .and()
                .withUser("test").password(new BCryptPasswordEncoder().encode("123")).roles("Vip1","Vip3");
    }
    
    //资源放行
    @Override
    public void configure(WebSecurity web) throws Exception {
        web.ignoring().antMatchers("/static/**");
    }
}
```



#### 连接数据库

```java
// --- 实体类\或者单独定义 LoginUser类实现UserDetails
public class SysUser implements UserDetails
// --- 业务处理类 实现 UserDetailService 接口，负责封装用于认证的用户信息
public class UserServiceImpl Implements UserDetails{
    @Autowired
    private UserMapper userMapper;
    
    public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
        //查询出用户信息 sysUser
        ....;
        //查询出用户权限 封装到 Collection<? extends GrantedAuthority> authorities
        ....;
        
        //返回封装好的用户信息，注意！ 该User是SpringSecurity的，还有其他重载方法
        return new User(sysUser.getUsername,sysUser.getPassword(),authorities);
    }
}

// --- SecurityConfig
public class SecurityConfig extends WebSecurityConfigurerAdapter{
    .....;
    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
        auth.userDetailsService(userService).passwordEncoder(passwordEncoder());
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

  

### 二、授权

#### 1.简单内存用户授权

见认证部分

#### 2. 基于方法的鉴权

```java
// -- 在 UserDetailService实现类中封装权限数据
// -- SecurityConfig开启方法保护支持
@Configuration
@EnableWebSecurity
@EnableGlobalMethodSecurity(prePostEnabled = true) // 开启访问接口前的权限校验
public class SecurityConfig extends WebSeccurityConfigurerAdapter{....}

// -- Controller写法
@RestController
public class UserController{
    
    @PermitAll //在 SecurityConfig中没有配置权限时可用
    @GetMapping("/user")
    .....;
    
    @PreAuthorize("hasAuthority('teacher')")
    @GetMapping("/admin")
    
    @PerAuthorize("hasRole('ROLE_USER')")
    ...;
    
    // 自定义鉴权逻辑 需要自定义鉴权类  @PreAuthorize("beanId.方法('权限名')")
    @PreAuthorize("checkPermission.hasPermission('权限名')")
    ....;
    
    @PreAuthorize("hasAuthority('student')")
    @PreFilter
}

// --- 自定义鉴权类
@Service("checkPermission")
public class PermissionService {
    public boolean hasPermission(String permission){
        // 拿到已经认证的信息
        Authentication authen = SecurityContextHolder.getContext().getAuthentication();
        for (GrantedAuthority authority : authen.getAuthorities()){
            if (authority.getAuthority().equals(permission))return true;
        }
        return false;
    }
}
```



### 四、使用Json登录

#### 1. 新增过滤器

```java
public class JsonLoginFilter extends UsernamePasswordAuthenticationFilter {
    @Override
    public Authentication attemptAuthentication(HttpServletRequest request, HttpServletResponse response) throws AuthenticationException {
//            如果接收到的参数是json格式，则进行解析
        if (request.getContentType().equals(MediaType.APPLICATION_JSON_UTF8_VALUE) ||
                request.getContentType().equals(MediaType.APPLICATION_JSON_VALUE)){
            ObjectMapper objectMapper = new ObjectMapper();
            UsernamePasswordAuthenticationToken token = null;
            try{
                ServletInputStream is = request.getInputStream();
                Map<String,String> params = objectMapper.readValue(is, Map.class);
                token = new UsernamePasswordAuthenticationToken(params.get("username"),params.get("password"));
            } catch (IOException e) {
                e.printStackTrace();
                token = new UsernamePasswordAuthenticationToken("","");
            }finally {
                setDetails(request,token);
                return this.getAuthenticationManager().authenticate(token);
            }
        }else {
//                非json格式则调用父类的方法来处理
            return super.attemptAuthentication(request,response);
        }
    }
}
```

#### 2.配置过滤器

```java
// SecurityConfig
http.addFilterAt(customAuthenticationFilter(),UsernamePasswordAuthenticationFilter.class);
JsonLoginFilter customAuthenticationFilter() throws Exception {
        JsonLoginFilter filter = new JsonLoginFilter();
        filter.setAuthenticationSuccessHandler((request, response, authentication) -> {
            response.setContentType("application/json;charset=utf-8");
            PrintWriter writer = response.getWriter();
            writer.write(mapper.writeValueAsString(new ResultInfo(null,200,"登录成功")));
            writer.flush();
            writer.close();
        });
        filter.setAuthenticationFailureHandler((request,response,authentication)->{
            response.setContentType("application/json;charset=utf-8");
            PrintWriter writer = response.getWriter();
            writer.write(mapper.writeValueAsString(new ResultInfo(null,200,"登录失败")));
            writer.flush();
            writer.close();
        });
        filter.setAuthenticationManager(authenticationManagerBean());
        return filter;
    }
```



### 五、JWT

```xml
<dependency>
    <groupId>io.jsonwebtoken</groupId>
    <artifactId>jjwt</artifactId>
    <version>0.9.1</version>
</dependency>
```



#### 1. 登录时进行认证，认证成功则返回token

```java
public class JwtLoginFilter extends AbstractAuthenticationProcessingFilter {
    protected JwtLoginFilter(String defaultFilterProcessesUrl, AuthenticationManager authenticationManager) {
        // 设置登录请求的路径
        super(new AntPathRequestMatcher(defaultFilterProcessesUrl));
        setAuthenticationManager(authenticationManager);
    }
    // 提取用户信息并进行认证
    @Override
    public Authentication attemptAuthentication(HttpServletRequest request, HttpServletResponse response) throws AuthenticationException, IOException, ServletException {
        //从请求中提取用户信息并封装
        User user = new ObjectMapper().readValue(request.getInputStream(), User.class);
        //封装认证token
        UsernamePasswordAuthenticationToken token = new UsernamePasswordAuthenticationToken(user.getUsername(), user.getPassword());
        //进行认证
        return getAuthenticationManager().authenticate(token);
    }
    //认证成功后执行该方法
    @Override
    protected void successfulAuthentication(HttpServletRequest request, HttpServletResponse response, FilterChain chain, Authentication authResult) throws IOException, ServletException {
        // 获取到用户权限
        Collection<? extends GrantedAuthority> authorities = authResult.getAuthorities();
        StringBuffer buffer = new StringBuffer();
        authorities.forEach((author) ->{
            buffer.append(author.getAuthority()).append(",");
        });
        // 生成jwt token
        String jwt = Jwts.builder()
                .claim("authorities",buffer.toString())
                .setSubject(authResult.getName())
                .setExpiration(new Date(System.currentTimeMillis()+60*10*1000))
                .signWith(SignatureAlgorithm.HS512,"jissi")
                .compact();
        response.setContentType("application/json;charset=utf-8");
        PrintWriter writer = response.getWriter();
        writer.write(new ObjectMapper().writeValueAsString(jwt));
        writer.flush();
        writer.close();
    }
    //认证失败后执行此方法
    @Override
    protected void unsuccessfulAuthentication(HttpServletRequest request, HttpServletResponse response, AuthenticationException failed) throws IOException, ServletException {
        response.setContentType("application/json;charset=utf-8");
        PrintWriter writer = response.getWriter();
        writer.write(new ObjectMapper().writeValueAsString(Results.loginFailed(failed.getMessage())));
        writer.flush();
        writer.close();
    }
}
```

#### 2. 带有token的请求，先检查token

```java
public class JwtTokenCheckFilter extends GenericFilterBean {
    @Override
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException {
        HttpServletRequest req = (HttpServletRequest) request;
        String jwtToken = req.getHeader("authorization");
        // 此处省略了token有效性检查，如果token为空应当处理
        // 从token中获取认证信息和权限
        Claims claims = Jwts.parser().setSigningKey("jissi")
                .parseClaimsJws(jwtToken.replace("Bearer", "")).getBody();
        String username = claims.getSubject();
        List<GrantedAuthority> authorities = AuthorityUtils
                .commaSeparatedStringToAuthorityList((String) claims.get("authorities"));
        UsernamePasswordAuthenticationToken token = new UsernamePasswordAuthenticationToken(username, null, authorities);
        SecurityContextHolder.getContext().setAuthentication(token);
        chain.doFilter(request,response);
    }
}
```

#### 3. 设置过滤器

```java
// SecurityConfig
http.addFilterBefore(
                new JwtLoginFilter("/login",authenticationManager()),
                UsernamePasswordAuthenticationFilter.class)
     .addFilterBefore(new JwtTokenCheckFilter(),UsernamePasswordAuthenticationFilter.class);
```



### 六、Oauth2

```java
spring-boot-starter-security
spring-security-oauth2
spring-boot-starter-data-redis
```

#### 1.配置授权服务器

```java
@Configuration
@EnableAuthorizationServer
public class AuthorizationServerConfig extends AuthorizationServerConfigurerAdapter {
    @Autowired
    private AuthenticationManager authenticationManager;
    @Autowired
    private RedisConnectionFactory connectionFactory;
    @Autowired
    private UserDetailsService userDetailsService;
    @Bean
    PasswordEncoder passwordEncoder(){
        return new BCryptPasswordEncoder();
    }
    @Override
    public void configure(ClientDetailsServiceConfigurer clients) throws Exception {
        clients.inMemory()
                .withClient("password") // client_id
                .authorizedGrantTypes("password","refresh_token") // 支持的授权模式:密码授权，刷新token
                .accessTokenValiditySeconds(1800) // token有序时间
                .resourceIds("rid") // 资源服务器id
                .scopes("all")
                // client_secret
                .secret("$2a$10$e8D9StZz6L2UrobOW7fBBO.OCBvViJ0FXAIw9/1CgFsqmSNEMh2.2"); 
    }
    
    @Override
    public void configure(AuthorizationServerEndpointsConfigurer endpoints) throws Exception {
        endpoints.tokenStore(new RedisTokenStore(connectionFactory)) //授权信息保存到redis
                .authenticationManager(authenticationManager)
                .userDetailsService(userDetailsService);
    }
    @Override
    public void configure(AuthorizationServerSecurityConfigurer security) throws Exception {
        security.allowFormAuthenticationForClients();
    }
}
```

#### 2.资源服务器

```java
@Configuration
@EnableResourceServer
public class ResourceServerConfig extends ResourceServerConfigurerAdapter {
    @Override
    public void configure(ResourceServerSecurityConfigurer resources) throws Exception {
        resources.resourceId("rid") //定义资源服务器id
                .stateless(true);
    }
    //配置各资源所需权限
    @Override
    public void configure(HttpSecurity http) throws Exception {
        http.authorizeRequests()
                .antMatchers("/admin/**").hasRole("admin")
                .antMatchers("/user/**").hasRole("user")
                .anyRequest().authenticated();
    }
}
```

#### 3. SecurityConfig

```java
@Configuration
@EnableWebSecurity
public class SecurityConfig  extends WebSecurityConfigurerAdapter {
    @Bean
    @Override
    protected AuthenticationManager authenticationManager() throws Exception {
        return super.authenticationManager();
    }
    @Bean
    @Override
    protected UserDetailsService userDetailsService() {
        return super.userDetailsService();
    }
    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
        // 配置内存用户
        auth.inMemoryAuthentication()
                .withUser("j")
                .password("$2a$10$e8D9StZz6L2UrobOW7fBBO.OCBvViJ0FXAIw9/1CgFsqmSNEMh2.2")
                .roles("admin")
                .and()
                .withUser("i")
                .password("$2a$10$e8D9StZz6L2UrobOW7fBBO.OCBvViJ0FXAIw9/1CgFsqmSNEMh2.2")
                .roles("user");
    }
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http.antMatcher("/oauth/**").authorizeRequests()
                .antMatchers("/oauth/**").permitAll();
        http.csrf().disable();
    }
}
```

#### 4. 启动Spring应用

```java
// 从授权服务器获取token
post http://localhost:8080/oauth/token?
    username=xx &
    password=xx &
    grant_type=password 授权模式 ：密码 &
    client_id=password &
    scope=all &
    client_secret=123
响应结果：
    {
        "access_token": "e3166a6c-a547-4c73-b556-be883aa7c86e",//token
        "token_type": "bearer",
        "refresh_token": "b1aff87b-b41a-4dc9-8a97-ad5ddee5acb5",//用于刷新token的token
        "expires_in": 1794, // token有效时长
        "scope": "all"
    }
// 访问资源
get http://localhost:8080/admin/?access_token=请求到的token
// 刷新token
post http://localhost:8080/oauth/token?
     grant_type=refresh_token &
     refresh_token=获取到的refresh_token &
     client_id=password &
     client_secret=123
响应结果：
     {
         "access_token": "e3166a6c-a547-4c73-b556-be883aa7c86e",
         "token_type": "bearer",
         "refresh_token": "b1aff87b-b41a-4dc9-8a97-ad5ddee5acb5",
         "expires_in": 1799,
         "scope": "all"
     }
```

