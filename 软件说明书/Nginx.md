# Nginx

高性能http和反向代理服务器，专为性能优化而开发

稳定，占用内存少，并发能力强

[TOC]

#### 1. 基本概念

* 反代理

  1. 正向代理

     代理用户，替代用户访问服务器

     ```shell
     需要在在浏览器中配置代理服务器
     ```

     

  2. 反向代理

     代理服务器，用户不能直接访问服务器(集群)，而是通过反向代理服务器转发到具体服务器

     ```shell
     对外隐藏服务器，只暴露反向代理服务器供用户访问
     ```

     

* 负载均衡

  服务器集群，由负载均衡将请求分发到服务器

* 动静分离

  加快网站解析速度，把动态文件和静态文件交给不同的服务器解析；

  需要额外的服务器专门存放静态资源，当请求静态资源时，Nginx直接将请求转发到静态资源服务器

* 高可用

  

#### 2. 安装&使用&配置文件

* linux安装

  ```shell
  # 相关依赖
  #pcre,openssl,zlib,nginx
  yum install nginx
  # 配置文件 /etc/nginx/nginx.conf
  ```

* 命令

  ```shell
  #查看版本号
  nginx -v
  #启动
  nginx
  systemc	start nginx
  #关闭
  nginx -s stop 
  systemc stop nginx
  #重新加载配置文件
  nginx -s reload #找到nginx安装目录
  ```

* 配置文件 /etc/nginx/nginx.conf

  ```shell
  # 三部分组成
  #======================全局配置============================
  # 全局：    配置文件开始~events , 设置nginx整体配置
  user nginx;
  worker_processes auto; 	# 允许产生的工作进程数量，并发处理量
  error_log /var/log/nginx/error.log;		# 错误日志路径
  pid /run/nginx.pid; 					# 进程pid存放路径
  include /usr/share/nginx/modules/*.conf;
  #=======================events==============================
  # events块     nginx服务器与用户网络的连接
  events {
      worker_connections 1024; # 每个工作进程的最大连接数
  }
  #=======================http==============================
  # http块        配置最频繁的部分包括 http全局块和server块
  http {
      access_log  /var/log/nginx/access.log  main;
      sendfile            on;
      tcp_nopush          on;
      tcp_nodelay         on;
      keepalive_timeout   65;
      types_hash_max_size 2048;
      include             /etc/nginx/mime.types;
      default_type        application/octet-stream;
      include /etc/nginx/conf.d/*.conf; # 文件引入
  #========================server子块=======================
      server {
          listen       80 default_server; #监听端口号
          listen       [::]:80 default_server;
          server_name  _; #主机名称
          root         /usr/share/nginx/html;
          include /etc/nginx/default.d/*.conf;
  		#location 块
          location / { # 路径配置
          }
          error_page 404 /404.html;
              location = /40x.html {
          }
          error_page 500 502 503 504 /50x.html;
              location = /50x.html {
          }
      }
   }
  ```

  

#### 3. 反向代理配置

* 效果：在浏览器输入地址，跳转到linux中Tomcat主页面

* 准备工作：nginx安装，tomcat安装

* 访问流程：www.box.com --> 访问到nginx 192.168.1.104:80  --> 转发到tomcat 192.168.1.104:8081

* 具体操作

  1. 修改host文件 指定 ip地址与域名映射

     ```shell
     # Windows c:\\Windows\System32\drivers\etc\hosts
     # Linux /etc/hosts
     192.168.1.104 www.box.com
     ```

  2. 在Nginx中配置请求转发（反向代理）

     ```shell
     # 修改 /etc/nginx/nginx.conf
     #http server段
      server{
          listen 80; #监听端口
          server_name 192.168.1.104; #主机名
          
          local /{
              proxy_pass http://193.168.1.104:8081;#代理/转发地址
          }
      }
     ```

  3. 访问 www.box.com显示tomcat页面

  4. 根据请求路径转发到不同tomcat

     在两个tomcat的webapps下创建 /edu/edu.html 和 /vod/vod.html

     ```shell
     # 新增server段
      server{
          listen 9000; #监听9000端口
          host_name 192.168.1.104;
          location ~ /edu/ { # ~表示使用正则匹配 ，包含/edu/的请求
              proxy_pass http://192.168.1.104:8081;
          }
          location ~ /vod/ {
              proxy_pass http://192.168.1.104:8082;
          }
      }
     ```

     #### location配置说明

     ```shell
     location [= | ~ | ~* | ^~] uri{...}
     =  用于不含正则表示式的uri，要求字符与uri完全一致
     ^~ 用于不含正则表达式的uri，nginx找到匹配度最高的location
     ~  用于包含正则表达式的uri，区分大小写
     ~* 用户包含正则表达式的uri，不分大小写
     ```

     

#### 4. 负载均衡配置

在客户端与服务器直接做负载均衡

* 效果：输入地址，访问时将请求平均分担到不同的tomcat

* 准备工作：两台tomcat，在webapps目录中都创建 /edu/edu.html

* 配置文件

  ```shell
  # http段添加upstream段
  http{
      upstream myservers{ #将负载均衡服务命名为 myserver
      	#默认使用轮询
          server 192.168.1.104:8081; #参与负载均衡的服务列表
          server 192.168.1.104:8082;
      }
      server{
          listen 80;
          server_name 192.168.1.104;
          location /{
              proxy_pass mysrevers;#将所有请求转发到负载列表
          }
      }
  }
  ```

  

  ##### 负载均衡策略

  ```shell
  1.轮询：默认使用
  ```

  ```shell
  2.权重weight：加在服务后
  http{
      upstream myservers{
           server 192.168.1.104:8081 weight=1;#权重默认为1
           server 192.168.1.104:8082 weight=10;#权重越大分配越多
      }
  }
  ```

  ```shell
  3. ip_hash：按照客户端ip的hash结果分配，每个客户端固定访问一个服务器
  # 可以解决session问题
  http{
      upstream myservers{
      	ip_hash;#指定使用iphash策略
          server 192.168.1.104:8081;
          server 192.168.1.104:8082;
      }
  }
  ```

  ```shell
  4. fair方式：根据后端服务器响应时间分配，响应时间短优先分配
  http{
  	upstream myservers{
          server 192.168.1.104:8081;
      	server 192.168.1.104:8082;
      	fair; #使用fair策略
  	}
  }
  ```

  

#### 5.  动静分离配置

把动态请求(请求数据)与静态请求(请求图片等)区分开

* 实现方式：1.将静态资源使用独立服务器，通过nginx转发；2.静态与动态混合，nginx来分开

* 通过location中指定后缀名实现请求转发，通过expires参数设置浏览器缓存静态资源的时间，缓存时间内会对比与服务器中的最后更新时间，如果不同还是会重新获取

* 准备：在系统中创建静态资源存储目录，让nginx指向目录

* 配置nginx

  ```shell
  #server段
  server{
      listen 80;
      server_name 192.168.1.104;
      location /static/{
          root /static/; # 访问本机 /static目录
          autoindex on; #直接访问/static时列出该目录下的文件
      }
  }
  ```

  

#### 6.  高可用集群配置

* 高可用：nginx可能宕机，使用多台nginx，主从模式

* 准备工作

  * 两台nginx服务器

  * keepalived   实现路由转发

    ```shell
    yum install keepalived
    ```

    

  * 虚拟ip地址



#### Nginx原理

* master进程和worker进程

  ```shell
  master负责将任务分配给worker
  
  客户端请求先到达master，master将任务分给worker，
  多个worker争抢任务，得到任务后请求tomcat
  ```

* 一个master多个worker的好处

  ```shell
  nginx -s reload 时 没有接到任务到的worker重新加载配置
  每个worker都是独立的进程，不需要加锁，有worker挂掉后不影响其他worker
  ```

* 设置的worker数量一般与cpu核心数相同

* worker连接数

  ```shell
  一个请求占用2个或4个worker连接(静态资源请求/响应[动态资源请求/响应])
  ```

* 一个master 四个worker（每个最大连接为1024）支持的最大并发数

  ```shell
  静态资源最大并发：worker_processces * worker_connections / 2； 反向代理/4
  ```

  