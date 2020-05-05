





#### 1. 日志框架

* 统一的日志接口层作为日志门面（日志的抽象层）；logging-abstract.jar

* 给项目中导入具体的实现

* 常见日志框架：JUL、JCL 、Jboss-logging 、logback 、log4j、slf4j...

  | 日志门面(日志抽象层)          | 日志实现                        |
  | ----------------------------- | ------------------------------- |
  | SLF4J、~~JCL、jboss-logging~~ | Logback、~~JUL、log4j2~~、log4j |

  SpringBoot底层是Spring框架，Spring框架默认使用CL；

* SpringBoot使用SLF4J 和 logback

​		

#### 2. SLF4j的使用

* 调用日志抽象层的方法而不是直接调用日志实现类
* 导入slf4j的jar 和 logback的实现jar

* 调用使用log4j和Jul需要额外的适配器（sjf4j出现比它们晚）
* 每个日志实现框架都有自己的配置文件，使用slf4j后还需要使用实现框架本身的配置文件

#### 3.遗留问题

项目中的其他框架（如 spring、mybatis、hibernate等）使用了其他体制框架（jboss-logging、commons-logging等）

* 统一日志框架，都使用slf4j需要中间适配层

  > commons-logging  替换为 jci-over-slf4j.jar
  >
  > log4j -> log4j-over-slf4j.jar
  >
  > JUL -> jul-over-slf4j.jar

  1. 不导入其他日志框架
  2. 导入中间包来替代其他日志框架
  3. 导入slf4j的实现

#### 4. SpingBoot 依赖spring-boot-starter-logging

spring-boot-starter-logging已经导入了中间包，springBoot能适配所有日志

还可以使用 spring-boot-starter-log4j2  二选一

<font color="red">引入其他框架时要移除它的日志框架依赖</font>

#### 5.* 日志使用

 ```java
Logger = LoggerFactory.getLogger(getClass());
// 日志输出级别由低到高：日志只会在当前级别和更高级别生效
// springBoot默认使用了info级别
logger.trace("trace日志");
logger.debug("debug日志");
logger.info("info日志");
logger.warn("warn日志");
logger.error("error日志");
 ```

配置文件修改日志级别

```properties
logging.level.com.jissi = trace
#com.jissi包内使用trace级别
#不写日志输出路径就输出到控制台
logging.path = /home/temp
#指定日志路径，/home/temp/spring.log
logging.file = log.log
#日志文件保存位置
logging.partten.console= 
logging.partten.file=
#日志在控制台和文件输出的格式
```

日志配置文件 classpath (resources/) logback-spring.xml 或 logback.xml

> logback.xml 直接被日志框架识别
>
> logback-spring.xml 可以使用profiles功能



#### 6. 切换日志框架

> exclude 原有的logback日志框架；
>
> exclude log4j中间包；
>
> 导入 log4j包（适配器自动导入）
>
> 添加log4j日志文件

或者切换为log4j2

> exceude spring-boot-starter-logging
>
> 导入 spring-boot-starter-log4j2