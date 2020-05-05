### Ribbon 负载均衡

实际生产环境中 同一个服务会有多个实例，构成集群

当请求到达时，具体使用哪一个实例需要使用负载均衡算法

##### Eureka中已经集成了Ribbon，提供轮询（默认）、随机等算法

#### 使用Ribbon

1. 在消费者向容器注入RestTemplate时加上 ```@LoadBalanced ``` 注解

   ```java
   @Bean
   @LoadBalanced//使用restTemplate时负载均衡
   public RestTemplate restTemplate(){
       return new RestTemplate();
   }
   ```

2. 消费者调用服务时使用 服务名称

   ```java
   url = "http://user-service/user/"+id;
   return restTemplate.getForObject(url,User.class);
   ```

   spring负载均衡自动配置类判断容器中存在RestTemplate，则开启负载均衡自动配置，向容器注入LoadBalancerInterceptor，

   restTemplate发送请求时被 LoadBalancerInterceptor拦截，到Eureka根据服务名称获取服务列表，根据负载均衡算法选择服务并使用服务url替换服务名称

#### 修改负载均衡算法为随机

```yml
user-service: #服务名称  
	ribbon:    
	NFLoadBalancerRuleClassName: com.netflix.loadbalancer.RandomRule
# 其他策略,共提供7种策略
# RoundRobinRule 默认，轮询
# RandomRule 随机
# RetryRule 默认轮询，如果轮询到的服务多次失效，就将该服务移出轮询列表
```

或者自定义配置

```java
@Configuration
public class Config{
    @Bean
    @LoadBalance
    public RestTemplate restTemplate(){
        return new RestTemplate();
    }
    //自定义负载均衡算法
    @Bean
    publi IRule randomRule(){
        return new RandomRule();
    }
}
```



#### 自定义负载均衡策略

* 实现IRule接口的类<font color='red'>使用@Configuration注解，但是不能在@ComponentScan所在包及子包下，即不能与启动类在同一个包</font>。，继承AbstractLoadBalancerRule简单一点

  ```java
  @Configuration
  public class MyRule extends AbstractLoadBalancerRule{
      //看看RandomRule之类的，改改源码
  }
  ```

  

* 启动类加注解@RibbonClient

  ```java
  //对指定的服务使用自定义策略
  @RibbonClient(name="指定微服务名",configuration=MyRule.class)
  @SpringBootApplication
  ```

  

