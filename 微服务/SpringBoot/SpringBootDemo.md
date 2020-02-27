<h1>SpringBootDemo</h1>

<center>contents</center>

[TOC]

---

#### 1. 最简web应用

* @SpringBootApplication ：核心注解，开启自动配置
* 项目入口：*Aplication类中的main方法

```java
//标记该类为SpringBoot启动类(该注解包含@ComponentScan)
@SpringBootApplication
@Controller//标记该类为Controller
public class HelloApplication {
    //web应用
    @RequestMapping("/hello")
    @ResponseBody
    public String sayHello(){
        return "Hello";
    }
    public static void main(String[] args) {
        //运行的应用一定要有@SpringBootApplication
        SpringApplication.run(HelloApplication.class);
        /*启动方法二        
        SpringApplication application = new SpringApplication(HelloApplication.class);
        application.run();
        */
    }
}
```



#### 2.@SpringBootApplication

```yaml
@SpringBootApplication 是组合注解包括 :
	@SpringBootConfiguration -> @Configuration -> @Component
	@EnableAutoConfigruation 根据项目中的jar包开启自动配置
	@ComponentScan 没有指定扫描路径，默认扫描所在的同级目录及其子目录
```

##### 关闭自动配置

```java
//关闭指定的自动配置
@SpringBootApplication(exclude = {RedisAutoConfiguration.class,...})
```

##### 自定义banner（启动时的spring标志）

将要显示的图标保存到banner.txt 放到resocures下

##### 关闭banner

```java
//启动应用的方式
SpringApplication application = new SpringApplication(HelloApplication.class);
application.setBannerMode(Banner.Mode.OFF);
application.run();
```



