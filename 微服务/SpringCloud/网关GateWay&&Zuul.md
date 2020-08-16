### GateWay

Spring Cloud Gateway组件的核心是一系列的过滤器，通过这些过滤器可以将客户端发送的请求转发（路由）到对 应的微服务。 

Spring Cloud Gateway是加在整个微服务前沿的防火墙和代理器，隐藏微服务结点IP端口信息，从 而加强安全保护。

Spring Cloud Gateway本身也是一个微服务，需要注册到Eureka服务注册中心。
网关的核心功能是：**过滤和路由**

一切对服务的请求都经过网关，然后再由 网关来实现 鉴权、动态路由等等操作。Gateway就是服务的统一入口，对外只暴露网关地址，隐藏内部微服务。

---

[TOC]





#### 核心概念

* 路由(Route)：路由id+目标URL+一组断言工厂+一组filter
* 断言(Predicate)：用来匹配请求url
* 过滤器(Filter)：对请求和响应进行修改处理，分为Gateway Filter和Global Filter



#### 使用Gateway

* 导入依赖 gateway、eureka-client

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-gateway</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
</dependency> 
```

* 启动程序

```java
@SpringBootApplication
@EnableDiscoveryClient
public ....{}
```

* 配置文件

```yml
spring:
  application:
    name: gateway
#代理服务来处理请求
  cloud:
    gateway:
      routes:
        - id: test1 #路由id 随意
#代理的服务所在的地址,可以直接使用http:..  , ...
          uri: http://127.0.0.1:8081
#路由断言，用来映射请求地址
          predicates:
            - Path=/user/** #不能有空格
#完整含义：将所有请求 /user/** 的请求都转发到http://127.0.0.1:8081

#eureka.client配置
```

#### 动态路由

* 使用eureka中的服务id作为uri，而不是写死
* uri: lb://user-service    使用负载均衡去eureka获取该服务

```yml
spring:
	application:
		name: gateway
	cloud:
		routes:
			- id: test1
			  uri: lb://user-service
			  predicates:
			  	- Path=/user/**
```

#### 使用过滤器为url处理前缀



* 补充前缀：...filterts : - PrefixPath=/user

  过滤器：PrefixPathGateWayFilter

```yml
spring: 
	cloud:
		routes:
			- id: test1
			  uri: lb://user-service
			  predicates:
			  	- Path=/**
			  filtes:
			  	- PrefixPath=/user
			  	# 处理后的请求 test/8 --> /user/8
```

* 去掉前缀：...filters: - StripPrefix=1 去掉第1段前缀

  过滤器：StripPrefixGateWayFilterFactory

```yml
.....
 routes:
 	- id: test1
 	  predicates:
 	  	- Path=/x/y/user/**
 	  filters:
 	  	- StripPrefix=2
 	  	# 处理后的请求 /x/y/user/8 --> /user/8
```



#### 过滤器 

##### 自带过滤器：GatewayFilterFactory接口的实现类

常见自带filter

> AddRequestHeader   给匹配的请求加上请求头
>
> AddRequestParameters  给匹配的请求加上请求参数
>
> AddResponseHeader  给匹配的响应加上响应头
>
> ...

```yml
		...
		filters:
			# 添加响应头 参数和值用逗号分隔
            - AddResponseHeader=test1,Abi
            - AddResponseHeader=test2,Aci
```



##### 过滤器类型：全局过滤器，局部过滤器

* 全局过滤器：作用在所有的路由上
  * 实现 GlobalFilter 接口，不在配置文件中配置
  * ```spring.cloud.gateway.default-filters ```需要在配置文件中配置，跟局部过滤器一样实现GatewayFilterFactory接口
* 局部过滤器：配置在具体路由下 ,针对具体路由起作用```spring.cloud.gateway.routes.filters ```

##### 全局默认过滤器

所有路由都要经过该过滤器(链)

```yml
spring:
  application:
    name: gateway
	#代理服务来处理请求
  cloud:
    gateway:
      default-filters:
		#全局默认过滤器
        - AddResponseHeader=globalFilter,y
      routes:
      	...
```

##### 执行生命周期

与SpringMVC的拦截器 pre、post类似，分为GateWFilterChain执行filter前后

##### 过滤器应用场景

* 鉴权：GateWayFilterChain执行Filter之前，检查请求权限，没有权限则直接返回
* 异常处理：GateWayFilterChain执行Filter之后，记录异常并返回
* 服务调用时长统计



##### 自定义过滤器

* 全局过滤器：@Component 并 实现 GlobalFilter接口 （Ordered接口，执行顺序）

```java
@Component
public class MyGlobalFilter implements GlobalFilter, Ordered {
    @Override
    public Mono<Void> filter(ServerWebExchange exchange
                            , GatewayFilterChain chain) {
        //模拟鉴权
        String token = exchange.getRequest().getQueryParams().getFirst("token");
        if (StringUtils.isBlank(token)){
            exchange.getResponse().setStatusCode(HttpStatus.UNAUTHORIZED);
            //直接返回响应
            return exchange.getResponse().setComplete();
        }
        exchange.getResponse().getHeaders().add("message","success");
        return chain.filter(exchange);
    }

    @Override
    public int getOrder() {
        return 1;//值越小越先执行
    }
}
```



* 局部过滤器 继承 AbstractGatewayFilterFactory<自定义过滤器.内部静态类Config>

```java
@Component
public class MyParamGatewayFilterFactory extends AbstractGatewayFilterFactory<MyParamGatewayFilterFactory.Config> {
    public static final String PARAM_NAME = "param";
    //将配置文件中的值封装
    public static class Config{
        private static String param;
        public String getParam(){return param;}
        public void  setParam(String param){this.param = param;}
    }
    public MyParamGatewayFilterFactory(){super(Config.class);}
    @Override
    public List<String> shortcutFieldOrder() {
        return Arrays.asList(PARAM_NAME);
    }
    //返回一个实现 GateWayFilter接口的类的实例
    @Override
    public GatewayFilter apply(Config config) {
        //lambda表达式 实现接口的filter方法
        return (exchange, chain) -> {
            ServerHttpRequest request = exchange.getRequest();
            //输出配置文件中配置的值
            if (request.getQueryParams().containsKey(Config.param)){
                request.getQueryParams().get(Config.param)
                        .forEach(value -> 
                                 System.out.printf("<--%s = %s-z-->",Config.param,value));
            }
            return chain.filter(exchange);
        };
    }
}
```



#### 负载均衡和熔断

gateway已经集成了ribbon和Hystrix，直接在配置文件修改参数即可





#### 跨域请求

网关是所有微服务的统一入口，同源策略可能导致js拿不到数据

* 同源：两个页面具有相同的协议（protocol），主机（host）和端口号（port）

* 同源策略：阻止一个域的javascript脚本和另外一个域的内容进行交互

* 跨域：使用javascript发送请求的主机  与接收请求的主机ip、port、协议不同



* GateWay允许跨域配置

```yml
spring:
    cloud:
        gateway:
          globalcors:
            cors-configurations:
              '[/**]': #匹配所有请求url
                allowedMethods:
                  - GET #放行get请求
    #           allowedOrigins: *  #*表示全部,放行所有
                allowedOrigins:
                  - "http://spring.io" #放行来自 http://spring.io 的Get请求
```



#### 高可用GetWay：网关集群

* 将多个网关注册到eureka
* 然后使用其他负债均衡来代理网关 如Nginx



#### GateWay与Feign的区别

* 网关负责接收所有用户请求，并分发给服务，多用于鉴权、服务端流量控制
* Feign负责服务内部相互调用



### Zuul

网关自身也要注册到eureka

导入依赖：spring-cloud-starter-netflix-zuul

主启动类加 @EnableZuulProxy   （@EurekaClient不用写了）

配置文件中设置端口、服务名、eureka等

通过网关访问微服务：http://网关地址/微服务名/restful请求

#### 映射规则

application.yml

```yml
zuul:
  prefix: /test #统一访问前缀
  ignored-services: provider-dept #忽略指定服务名(不再允许使用服务名访问)，忽略所有使用 "*" 包括引号
  
  #通过uri实习路由
  routes: #定义路由
  	dept-service:
  		path: /dept/**  #访问时必须带上/dep/前缀
  		url: http://127.0.0.1:8080  
  
  #结合eureka实现路由
  routes:
  	provider-dept: /dept/** #直接 使用服务名: 访问前缀
  	# 或者单独指定
    dept-service: #路由名，随意
    	serviceId: provider-dept #微服务应用名
    	path: /dept/** #映射/访问路径   访问时必须带上/dep/前缀
```

