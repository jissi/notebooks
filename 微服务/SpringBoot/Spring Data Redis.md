<h1>Spring Data Redis</h1>
[TOC]

---

```xml
<groupId>org.springframework.boot</groupId>
<artifactId>spring-boot-starter-data-redis</artifactId>

<groupId>org.apache.commons</groupId>
<artifactId>commons-pool2</artifactId>
```

配置redis和连接池

```yaml
spring:
#  redis配置
  redis:
    host: 123.56.158.88
    password:
    database: 0
    port: 6379
#    连接池配置
    lettuce:
      pool:
        min-idle: 5
        max-idle: 8
        max-active: 8
        max-wait: 1ms
      shutdown-timeout: 100ms
```

#### 1. SpringBoot自动配置：RedisTemplate 与 StringRedisTemplate

* 自动配置配置了两个redisTemplate，两者数据不共通
* RedisTemplate：JDK序列化，键值都以字节数组方式存储
* StringRedisTemplate：String序列化，继承了上面的，键值都以字符方式存储

#### 2. 自定义redisTemplate，使用jackson序列化

```java
@Configuration
public class RedisConfig {
    @Bean
    public RedisTemplate<String, Object> redisTemplate(RedisConnectionFactory factory) {
        RedisTemplate<String, Object> template = new RedisTemplate();
        template.setConnectionFactory(factory);
        //一定要设置ObjectMapper 否则从缓存拿回的值是 LinkedHashMap无法转回对象
        Jackson2JsonRedisSerializer jackson2JsonRedisSerializer = new Jackson2JsonRedisSerializer(Object.class);
        ObjectMapper om = new ObjectMapper();
        om.setVisibility(PropertyAccessor.ALL, JsonAutoDetect.Visibility.ANY);
        om.enableDefaultTyping(ObjectMapper.DefaultTyping.NON_FINAL);
        jackson2JsonRedisSerializer.setObjectMapper(om);
        
        StringRedisSerializer stringRedisSerializer = new StringRedisSerializer();
        // key采用String的序列化方式
        template.setKeySerializer(stringRedisSerializer);
        // hash的key也采用String的序列化方式
        template.setHashKeySerializer(stringRedisSerializer);
        // value序列化方式采用jackson
        template.setValueSerializer(jackson2JsonRedisSerializer);
        // hash的value序列化方式采用jackson
        template.setHashValueSerializer(jackson2JsonRedisSerializer);
        template.afterPropertiesSet();
        return template;
    }
}
```



#### 3. redisTemplate相关操作

* 操作具体数据类型的对象封装在redisTemplate.opsForXXX()

  ```shell
  # opsForValue() 操作String类型的对象
  # opsForList()
  # opsForHash()
  # opsForSet()
  # opsForZSet()
  ```

  

