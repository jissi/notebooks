<h1>JavaConfig</h1>
Spring1.x时代使用xml配置 -> 2.x时代 可以使用注解 -> 3.x到4.x 提供Java配置

---
<center>Contents</center>
[TOC]

---

### 一、 核心注解

* IOC容器有BeanFactory 和 ApplicationContext(常用)

* @Configuration 作用于类上 该类相当于xml

* @Bean 作用在方法上 相当于bean标签

* @ComponentScan 没有指定扫描路径时，默认扫描所在的同级目录及其子目录

  ```java
  //标记该类为配置类 相当于xml文件
  @Configuration
  @Import(value = {Config1.class,Config2.class})) //引入其他javaConfig
  @ImportResource({"classpath:context.xml"}) //引入xml配置文件
  // 如果要在xml中引入javaConfig，只需要将该配置类写在<bean>标签(<import>标签只能引入xml)
  //包扫描 扫描@Service等组件将其加入IOC容器
  @ComponentScan(basePackages = "com.test.Service")
  public class SpringConfig {
      //标记将改方法的返回对象加入IOC容器
      @Bean
      @Scope(ConfigurableBeanFactory.SCOPE_SINGLETON) //bean作用域，默认单例
      public UserDao getUserDao(){
          return new UserDao();
      }
      //这里举个例子，只要roleService的实例被Spring托管（如通过包扫描等方式创建的实例）
      //假设roleSerice是在xml中配置的，该方法的参数就能自动注入
      @Bean
    public RoleDao roleDao(RoleService roleService){
          return new RoleDao( roleService );
    }
  }
  -------------------------------------------------
  //加载配置类
  AnnotationConfigApplicationContext context = new AnnotationConfigApplicationContext(SpringConfig.class);
  context.getBean(xxx.class);
  ```
  
  

#### 注入外部值

* @PropertySource(value={"classpath:xxx.properties",...}) 用在类上

  该文件的内容加载到spring 的  Environmen 中

  如果配置文件不存在 可以忽略，加参数： igonreResourceNotFound=true

* @Value(“${name}”) 用在属性上赋值

  ```java
  //指定外部配置文件
  @PropertySource(value = {"classpath:jdbc.properties","classpath:notFound"},ignoreResourceNotFound = true)
  public class TestProps{
      @AutoWired
      Environmen env;
      //将配置文件的值注入到属性
      @Value("${url}")
      private String url;
}
  ```
  
  

### 二、高级装配

#### 1. @Profile  && 处理多个bean同类型时注入歧义

```java
@Configuration
public class SpringConfig{
    
    @Bean
    @Primary // 当出现多个CD接口的bean时，指定当前为自动转配时的唯一bean，也可以与@Component一起用
    @Profile("dev")//在dev环境下该bean生效
    public CD cd(){return new CD();}
    
    @Bean//未指定profile，所有环境都生效
    public Player player(){return new Player();}
}
//----测试类
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(classes = SpringConfig.class)
@ActiveProfiles( {"dev","pro"}) //使用dev和pro环境
public class Main {
    @Autowired
    @Qualifier("bean Id")// 当出现多个bean或指定了多个@Primary到转配歧义时，使用@Qualifier指定bean
    private CD cd;
    ...
}

// --------歧义解决方案2
//使用 自定义注解 替代 @Qualifier("beanId")
@Target({ElementType.CONSTRUCTOR, ElementType.METHOD,ElementType.PARAMETER, ElementType.FIELD})
@Retention(RetentionPolicy.RUNTIME)
@Qualifier
public @interface @BeanId{}
```

#### 2. @Conditional

```java
@Profile注解就是通过@Conditional实现的
@Conditional(ProfileCondition.class)
public @interface Profile
```

```java
使用@Conditional模拟@Profile
public class MyCondition implements Condition {
    @Override
    public boolean matches(
            ConditionContext context,//用来获取应用的各种环境
            AnnotatedTypeMetadata metadata //用来获取当前位置上的注解信息
       ) {
        String[] profiles = context.getEnvironment().getActiveProfiles();
        for (String profile : profiles){
            if (profile.equals("dev")){
                return true; //返回true，表示条件生效，该bean注解生效
            }
        }
        return false;
    }
}
// 将上面的javaConfig配置类的
//@Profile(value="dev") 改为 @Conditional( MyCondition.class)即可
```



### 三、AOP

```java
// 切面类
@Aspect
public class Aspect{
    // 定义切点
    @Pointcut("execution( * base.*.perform(..))")
    public void pointcut(){}
    // 前置通知 ，注解属性可以写新的切点表达式，也可以直接使用定义的切点
    @Before("pointcut() && args(info)") // 接收切点参数
    public void before(Striing info){ sout( info );}
    @AfterReturning("pointcut()")// 后置通知，切点执行正常后触发
    @AfterThrowing("pointcut()")// 异常通知，切点方法执行异常时触发
    @After("pointcut()")//最终通知，无论切点执行是否正常都触发，在后置通知之后
    ...;
}
// 配置类 开启 @EnableAspectJAutoProxy  (xml中的 <aop:aspectj-autoproxy/> )
@Configuration
@EnableAspectJAutoProxy
public class SpringConfig {
    @Bean
    public Aspect aspect(){return new Aspect();}
}
```



### 四、SpringMVC



