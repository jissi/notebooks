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

<font color=red>redis所有操作都是原子性的，采用单线程处理所有业务，，命令一个一个排队执行，无须考虑并发操作带来的数据影响</font>

<font color="red">KEY的命名规范：```表名：主键名：主键值：字段名```</font>

#### 1. String类型

##### 取值、赋值、删除

```properties
#设置一个键值对
set key value
#获取一个值
get key
#删除一个键值对
del key
#获取值的长度
strlen key
#给key追加值
append key value
#一次存多个键值对
mset k1 v1 k2 v2 k3 v3
#一次取多个值
mget k1 k2 k3


# 数据未获取到 返回 nil 等同于 null
# 最大存储容量 512M
```

数据量过大大时考虑切分后mset

##### 数值增减（解决mysql分表时ID重复的问题）

如果原始String不能转成数值或者超过数值（Long.MAX_VALUE）上限将报错

```shell
incr key1 : key1的值自增，可以为负值
decr key1 : key1的值自减
incrby key1 10 : 将key1的值+10
incrbyfloat key1 1.5:增加小数
decrby key1 10 : 将key1的值-10
```

#### 设置数据的生命周期

```properties
#存活10s
setx key1 10 value
#存活10ms
psetx key1 10 value
#设置后再操作该key则时长失效
```

String应用场景

微博粉丝数

```shell
#方法一：以用户主键和属性作为key
# 表名:主键名:主键值:字段名
set user:id:10001:fans 5000 #粉丝 5000
set user:id:10001:blogs 200 #博客 200

#方法二：使用json格式
set user:id:10001 {id:10001,name:test,fans:5000,blogs:200}
```

各种结构型和非结构型数据

#### 2. hash类型(存储数据库中的数据对象（redis里的redis）)

使用String存储JSON只方便取值不方便修改

用来存储对象

> key
>
> > field1    value
> >
> > field2   value

hash存储上线 2的32次方-1；

hash类型十分贴近对象的数据存储格式，但是hash设计初衷不是为存储大量对象而设计的，不要滥用，更不可以将hash作为对象列表使用；

hgetall再field很多时效率会非常低；

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
hincrby user1 age 1 # user1的age+1
hlen user #获取key中的field数量
hexists user age#是否存在某个field
hkeys user #获取所有field
hvals user #获取所有value
hsetnx key field value #如果field没值则设置value
```

#### 购物车

一个用户一个购物车、一个购物车可以有多个商品、商品购买数量

```shell
field1 保存 商品id:nums 200
专门保存商品的hash
```

抢购

```shell
商家id做key，
价格做field,
数量做value
```

#### 3. list类型（先进后出，适用与评论列表，多数据、有顺序）

使用LinkedList实现（底层双向循环链表）

更确切的描述是栈（先进后出）

list的数据都是String类型，容量2的32次方-1

具有索引的概念，但是操作数据时通常以队列的形式出入对

##### 命令

```shell
lpush key1 1 2 3 # 从左边存入一列
# 从右边存入一列
rpush key1 4 5 6 
# 从左边取出列
lrange key1 0 2 :取出0～2索引的值 先进后出！！！
lrange key1 0 -1 :取出所有
lpop key1 # 弹出左边列最上面的元素
rpop key1 # 弹出右边列最上面的元素
lrem key1 数量 value #移除指定数量的value
llen key1 # 获取元素个数
lindex key1 0 #指定查索引的值
blpop key1 [key2] 30 #30秒内lpop,如果30秒内都没有数据，没有数据就不等了
brpop key1 [key2] 30
```

微信点赞显示顺序

```java
动态id做key
点赞：rpush
取消点赞：lrem
```

关注列表和粉丝列表

多服务器日志列表，redis实现消息汇集

#### 4. set类型（无序，不重复，基于Hash结构）

List的链表结构存储效率低

Set 大量存储，便于查询

##### 命令

```shell
sadd skey1 1 2 3 # 向集合添加元素
smembers skey1 # 查看集合中所有元素
srem skey1 1 # 从集合移除元素
scard key1 #获取数据总量
sismember skey1 value # 查询集合中是否存在元素
srandmember key1 数量 #随机取出指定个数量的数据
spop key1 数量 #随机pop指定个数据
#集合运算
# 差集 A - B (A中存在，B中不存在)
sdiff skey1 skey2
# 交集
sinter skey1 skey2
# 并集
sunion skey1 skey2
```

随机推荐

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