### Hystrix

Netflix开源的 延迟、容错库，用于隔离访问远程服务、第三方库，避免出现级联失败

[TOC]

#### 雪崩

* 微服务之间可能相互调用，如果其中一个服务出现问题，将导致整个请求阻塞，请求不结束，tomcat不释放线程；

* 如果此时有更多访问该服务的请求，将都陷入阻塞
* 服务器支持的线程和并发量有限，请求一直阻塞将导致服务器资源耗尽，无法再提供任何服务，形成雪崩效应

#### Hystrix解决雪崩问题：线程隔离&服务降级

* 线程隔离：为每个依赖服务分配一个线程池，线程池满则直接拒绝访问，默认不允许排队(缩短失败判断时间)

  用户请求不再直接访问服务，而是通过线程池中的空闲线程，线程池满或者请求超时，就会触发服务降级

* 服务降级：优先保证核心服务，其他服务不可用或弱可用

  服务降级会导致请求失败，但不会导致阻塞，最多应该改依赖服务线程池中的资源，不会对其他服务造成影响

* 导入相关依赖

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-hystrix</artifactId>
</dependency>
```

* 在启动类上加上 ```@EnableCircuitBreaker```

  ```java
  @SpringBootApplication
  @EnableDiscoveryClient
  @EnableCircuitBreaker
  //三个注解组成  -》》 @SpringCloudApllication
  ```

* 在Controller编写降级逻辑：方法签名必须与要降级的方法一致

  ```java
  //编写备用方法，熔断后、出现异常就 返回执行备用方法
  // 返回类型、参数列表必须与 getUser一致
  public String fallback(Long id){
      return "请求超时";
  }
  //为该服务指定备用方法
  @HystrixCommand(fallbackMethod = "fallback方法名")
  @GetMepping("/{id}")
  public String getUser(@PathVariable("id") Long id){...}
  ```

* 使用全局默认FallBack

  为了避免针对每个方法编写fallback，使用全局fallback可以为当前类的所有方法设置fallback方法

  ```java
  @Controller
  @RequestMapping("/")
  //指定默认的fallback方法
  @DefaultProperties(defaultFallback = "fallback")
  public class xx(){
      
      //无参
      public String fallback(){
      	return "请求超时";
      }
      
      @HystrixCommand//每个方法都要加该注解
      @GetMepping("/{id}")
      public .....{}
  }
  ```

* 指定调用服务的超时

  默认1秒，一秒后未能调用服务成功，将触发降级

  ```yml
  #设置服务请求超时 2秒
  hystrix:
    command:
      default:
        execution:
          isolation:
            thread:
              timeoutInMilliseconds: 1000
  ```

####  服务熔断

Hystrix的服务熔断机制，可以实现弹性容错；当服务请求情况好转之后，可以自动重连。

通过断路的方式，将后续 请求直接拒绝，一段时间（默认5秒）之后允许部分请求通过，如果调用成功则回到断路器关闭状态，否则继续打 开循环。



Hystrix熔断状态机的三种状态

* closed：断路器关闭，所有请求都正常访问
* half open：半开，open后先休眠5秒(5秒内处于断路状态)，然后进入半开状态，此时允许部分请求通过，如果请求成功则close，请求失败则 open,再休眠5秒
* open：断路器打开，所有请求被拒绝

```yml
hystrix:
  command:
    default:
      circuitBreaker:
        errorThresholdPercentage: 50 # 触发熔断错误比例阈值，默认值50%
        sleepWindowInMilliseconds: 5000 # 熔断后休眠时长(熔断多少秒后去尝试请求)，默认值5秒
        requestVolumeThreshold: 5 # 熔断触发小请求次数/10s，默认值是20
      execution:
        isolation:
          thread:
            timeoutInMilliseconds: 2000 # 熔断超时设置，默认为1秒
            
#该配置下，10秒内触发5次异常将进入熔断
```



#### 服务降级：客户端实现

服务降级需要在客户端/调用方实现，由feign支持

新建实现FallbackFactory<feign接口>，添加@Component注解 ，完成对feign调用相应服务时的降级处理

查看feign部分



#### HystrixDashboard 监控服务状态

![](G:\data\notebooks\resources\img\hystrix.png)