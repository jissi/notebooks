SpringBoot使用Redis为例

* 解决的是Bean注入问题



Enable* 注解：打开相应的功能：相应Bean的注入和相应配置初始化

starter ：提供一种标准

装配的核心：JavaConfig 实际上就是Spring framework中的无配置化，



@Configuration声明一个配置类等价与xml的beans的定义



通过JavaConfig来取代xml



@Component注解被@ComponentScan扫描，不是配置类

再一个配置文件中导入另一个配置文件时使用@Import



JavaConfig相关注解

```java
@Configuration//声明配置类
@Import(OtherConfig.class)//在当前配置类加入另一个配置类
@Conditional(实现Condition接口的类.class)//注入IOC的前提
@Bean//将return的对象添加到LOC
```



SpringBoot核心功能

* 自动装配 EnableAutoConfiguration
* 开箱即用
* Actuatorjiank
* SpringBootCLI



Spring Boot Starter核心：自动装配 -> 实现Bean的自动装配





导入spring-boot-starter-data-redis后就可以直接 @AutoWired RedisTemplate;

##### 自动配置类什么情况下会被加载

官方：sprng-boot-starter-xx

非官方：xx-spring-boot-starter

* @ConditionalOnClass(xxx.class)  引入官方提供的starter（包含xxx类）则：classpath下存在xxx类触发条件满足，进而自动装配

  <font color=red>即通过引入jar的方式触发自动装配</font>

* 如果是第三方则需要第三方提供AutoConfiguration

  

官方与第三方都遵循标准否则

LOC容器扫描某个包路径下的配置类实现自动装配：

* 如何扫描不同starter中的配置类（包路径不一样，怎么识别）？
* 官方提供自动配置与自己写的冲突时如何选择性的装配？



### 选择性装配

ImportSelecor接口 其中的方法返回了需要被loc管理的类的数组 {xxx.class,yyy.class} ;

```java
@Override
public String[] selectImports(....){
    return new String[]{xxx.class}
}
```

如果返回starter中的自动配置类则可以自动装配

自动配置类从哪里获得？

Spring FactoriesLoader扫描classpath*目录下/META-INF/spring.factories文件，获取指定key的value  ，values[] -> 配置类

```@EnableAutoConfiguration注解中有一个@Import(实现ImportSelecor的类.class)```



@SpringBootApplication()

### SPI(Java中提供)





手写starter

* 引入 spring-boot-stater

* 写一个AutoConfiguration

* resources/META-INF/spring.factorues

  ```properties
  spring.springframework.boot.autoconfigure.EnableAutoCOnfiguration=com.xxxx.AutoCOnfiguration
  ```

  