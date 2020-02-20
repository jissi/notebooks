<h1>Redis</h1>
意大利创业公司，后来被VMware赞助，底层使用C语言

应用领域：（数据量大，并发性高）分布式缓存；分布式Session；保存博客或留言回复；

数据存储到内存，存取快

对持久化支持不够好，一般不作为主数据库存储，一般配合关系型数据库使用

[TOC]

### 一、redis简介

#### 1. 非关系型数据库

nosql（Not Only SQL）数据库（非关系型数据库）;

<font color="green">关系型数据库以 二维表的形式存储数据；</font>

<font color="green">非关系型数据库以 键值对存储数据</font>

#### 2. NoSQL数据库分类

* 键值存储数据库：redis... 内存缓存
* 列存储数据库
* 文档型数据库：MongoDB... 存储大文本
* 图形数据库：存储地质信息数据



### 二、安装Redis

#### 1. 下载redis源码包

#### 2. 安装C语言编译器 gcc-c++

#### 3. 编译redis

```shell
cd /usr/local/src/redis
make 
# 报错：
#compilation terminated. make[1]: *** [adlist.o] Error 1 make[1]: Leaving directory `/usr/local/src/redis/redis-5.0.5/src' make: *** [all] Error 2
# 解决方法：yum install gcc
make MALLOC=libc
# 安装，指定安装路径
make PREFIX=/usr/local/redis
```

#### 4. 安装目录下的可执行文件（/usr/local/redis/bin/）

```shell

redis-cli  # 启动 redis 客户端
redis-server # 启动 redis 服务端

redis-benchmark  
redis-check-aof  
redis-check-rdb  
redis-sentinel  
```

#### 5. 启动Redis

* 前端启动：./redis-server

* 后端启动：./resdis-server  redis.conf

  ```shell
  # 拷贝编译完生成的 redis.conf 到 安装目录的bin/ 下
  cp /usr/local/src/redis/redis5/redis.conf /usr/local/redis/bin/
  # 修改配置文件权限
  chmod 777 redis.conf
  #修改 daemonize no 设置为yes 开启后台运行
  注释掉 bind 127.0.0.1
  设置 protected-mode no
  ```

#### 6. 关闭Redis

* 暴力关闭：kill pid

* 安全关闭：./redis-cli  shutdown

  先保存数据再关闭



### 三、Jedis（相当于JDBC）

#### 1. 基本使用

​	导入jar包：commons-pool2-2.3 、jedis-2.7.0

​	使用Jedis

* 直接连接

  ```java
  // 创建和Redis的连接
          Jedis jedis = new Jedis("192.168.1.106",6379);
          jedis.set("key2","2");
          System.out.println(jedis.get("key2"));
          jedis.close();
  ```

  

* 使用连接池 <font color="red">用完要关闭</font>

  ```java
  // 创建redis连接池
          JedisPool jedisPool = new JedisPool("192.168.1.106",6379);
          // 获取连接
          Jedis resource = jedisPool.getResource();
          System.out.println(resource.get("key2"));
          resource.close();
  ```



#### 2. 整合Spring

* 配置文件

  ```xml
      <beans>
          <!--连接池配置-->
          <bean id="jedisPoolConfig" class="redis.clients.jedis.JedisPoolConfig">
              <!--一些参数-->
          </bean>
          <!--创建连接池-->
          <bean id="jedisPool" class="redis.clients.jedis.JedisPool" destroy-method="clone">
              <constructor-arg name="poolConfig" ref="jedisPoolConfig"/>
              <constructor-arg name="host" value="192.168.1.106"/>
              <constructor-arg name="port" value="6379"/>
          </bean>
      </beans>
  ```

* 获取连接池

  ```java
      private ApplicationContext applicationContext;
      @Before
      public void beforeTest(){
          applicationContext = new ClassPathXmlApplicationContext("classpath:applicationContext.xml");
      }
      
      @Test
      public void test(){
          JedisPool jedisPool = (JedisPool) applicationContext.getBean("jedisPool");
          Jedis resource = jedisPool.getResource();
          System.out.println(resource.get("key2"));
          // spring回负责关闭
      }
  ```



### 四、Redis数据类型 5种

> 字符串：String
>
> 列表：list
>
> 散列：Hash
>
> 集合：set
>
> 有序集合：zset



#### 1. String类型

##### 取值、赋值、删除

* set key value
* get key  空值为 nil
* del key 返回删除的value

##### 数值增减

```shell
incr key1 : key1的值自增
edcr key1 : key1的值自减
incrby key1 10 : 将key1的值+10
decrby key1 10 : 将key1的值-10
```



#### 2. hash类型(存储数据库中的数据对象)

用来存储对象

##### 命令:

```shell
# 存储一个user对象 user1
hset user1 name jissi
hset user1 age 20
hset user1 sex male
# 一次存储多个属性
hmset user1 name jissi age 20 sex male

# 获取一个属性
hget user1 name
# 获取所有属性
hgetall user1
# 一次获取多个属性
hmget user1 name age sex


# 删除多个属性
hdel user1 name sex

# 增减
hincrby user1 age 1 : user1的age+1
```



#### 3. list类型（先进后出，适用与评论列表）

使用LinkedList实现（底层双向循环链表）

更确切的描述是栈（先进后出）

##### 命令

```shell
# 从左边存入一列
lpush key1 1 2 3
# 从右边存入一列
rpush key1 4 5 6
# 从左边取出列
lrange key1 0 2 :取出0～2索引的值 先进后出！！！
lrange key1 0 -1 :取出所有

# 弹出左边列最上面的元素
lpop key1
# 弹出右边列最上面的元素
rpop key1

# 获取元素个数
llen key1
```



#### 4. set类型（无序，不重复）

##### 命令

```shell
# 向集合添加元素
sadd skey1 1 2 3
# 查看集合中所有元素
smembers skey1
# 从集合异常元素
srem skey1 1
# 查询集合中是否存在元素
sismember skey1 1 : 0表示不存在

#集合运算
# 差集 A - B (A中存在，B中不存在)
sdiff skey1 skey2
# 交集
sinter skey1 skey2
# 并集
sunion skey1 skey2
```



#### 5. zset类型（有序集合，榜单）

##### 命令

```shell
# 添加元素 score memerber [...]  根据score从小到大排序
zadd zkey1 10 jissi 10 jsk
# 根据分数排序列出元素[显示分数]
zrange zkey1 0 -1 [withscores]
# 降序排序
zrevrange zkey1 0 -1 [withscores]
# 查看分数
zscore zkey1 jissi
# 删除元素
zrem zkey1 jissi
```



### 五、通用命令

* keys * : 查看所有key   keys *1 所有以1结尾的key
* ping ：服务器是否在线 返回pong



### 六、Redis持久化方案

* RDB持久化（默认方案）

  ```shell
  # 启动时从 dump.rdb 恢复数据
  ```

  

  * 特点：可以设置多长时间保存一次
  * 优点：Redis数据存取速度快
  * 缺点：服务器断电时会丢失部分数据，数据的完整性得不到保证

* AOF持久化

  修改配置文件

  ```shell
  appendonly yes
  
  # 启动时从 appendonly.aof中恢复数据
  ```

  * 可以设置实时保存
  * 优点：持久化良好，能够保证数据的完整性
  * 缺点：大大降低redis存取速度





### 七、集群中的主从复制

两台机器主从相互备份，心跳检测机制：从机向主机发送ping，如果主机没有回复，则从机开始提供服务

<font color="red">从机提供的服务只读，不允许增删改</font>

必须使用 rdb 备份方式

##### 配置从机，主机无需变动

```shell
复制主机的 /bin 下的所有内容到从机目录
修改从机配置文件
vim redis.conf
# 修改端口号
port 6380
# 设置主机
replicaof 192.168.1.106 6379
# 或者 slaveof 


# 测试从机
./redis-cli -p 6380 # -p 指定端口号
```



* 完整复制
  1. 从机向主机发送sync命令
  2. 主机将  dump.rdb  文件发送给从机
  3. 主机redis数据变动：发送缓冲获得所有写命令