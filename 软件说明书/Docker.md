<h1>Docker</h1>
开源应用容器引擎，轻量级容器技术；

安装配置好的的程序打包成Doucker镜像，下次安装安装镜像即可；

运行中的镜像称为容器

[TOC]

---

### 一、Docker核心概念

* docker主机（host）：安装了Docker的主机
* docker客户端（client）：连接docker主机进行操作
* docker仓库（registry）：保存打包好的软件镜像
* docker镜像（images）：软件打包好的镜像，放在仓库中
* docker容器（container）：镜像启动后的实例称为一个容器





1. 安装Docker
2. 去Docker仓库找打软件镜像
3. 使用Docker运行镜像，生成容器
4. 容器的启动和停止就是对软件的启动和停止



### 二、Docker安装和使用

#### docker命令

```shell
docker -v 查看dokcer版本
systemctl start docker 启动docker
```



### 三、镜像管理

镜像站：https://hub.docker.com/

#### 1. 镜像基本操作

```shell
使用docker镜像加速：www.docker-cn.com
docker search mysql #搜索镜像
docker pull mysql:5.5 #拉取镜像[:标签|默认latest]

docker image tag nginx:latest nginx:imported # 修改镜像标签

docker images <=> docker image ls  #查看镜像 
docker image ls -q # 列出所有镜像id
docker images inspect 镜像名:5.5 # 查看镜像详情

docker rmi IMAGE ID <=> docker image rm 镜像ID #删除镜像（根据镜像id）
docker rmi -f 镜像id # 强制删除镜像(强制删除正在使用的镜像)
docker rmi -f `docker images -q` #删除所有镜像
```



#### 2. 镜像导入导出

```shell
docker image save 镜像id > 文件.tar # 镜像导出为STDOUT(标准输出)
docker image save -o nginx.tar nginx:latest # 将nginx:latest 导出为tar格式
docker image load < 文件.tar # STDOU镜像需要使用标准输入(STDIN)方式导入
docker image load -i nginx.tar # tar格式镜像导入

docker image tag nginx:latest nginx:imported # 修改镜像标签

```



### 四、容器管理

一个镜像可以启动多个容器

#### 1. 创建并启动容器（交互式启动&守护式启动）

```shell
# 参数说明
# 交互模式启动 -it i:STDIN接收标准输入 t:分配虚拟终端tty  启动容器后进入容器终端，退出终端容器停止
# 守护模式启动 -d daemon 放入后台运行 容器启动后不会直接进入容器终端，不使用-d 容器终端将在前台运行
# --name CentOS 指定容器名为CentOS
# -p 宿主机端口:容器端口 将宿主机端口映射到容器，可以写多个-p 指定多个映射关系
# -v 挂载数据卷 -v /opt/html:/var/html 将宿主机/opt/html挂载到容器/var/html 可以使用多个-v
# --rm 退出容器时自动删除容器(多用于交互式容器)
# 镜像不存在时会自动pull

# 交互式容器 一般用于开发、测试等临时性任务
docker [container] run -it --name centos  镜像名 # 执行后进入centos终端，退出终端容器停止
docker run -it centos --rm 镜像名 # 退出容器时自动删除该容器

# 守护式容器 用于网络服务，必须指定端口映射，否则只能在宿主机访问
docker [container] run -d --name nginx -p 8080:80 镜像名 
# 以守护方式启动，后台提供服务,宿主机8080端口映射到容器80端口

# 创建容器时实现文件映射（挂载）
docker container run --name nginx -d -p 8080:80 -v /opt/html:/usr/local/nginx/html nginx:latest
```



#### 2. 容器监控

```shell
docker ps <=> docker container ls # 查看正在运行的容器
docker ps -a <=> docker container ls -a # 查看所有容器

docker [container] inspect nginx # 查看容器详情
# 使用 -f 参数查看指定信息
docker inspect -f=‘{{.Name}} {{.ID}}’ 容器名 #查看容器名和ID

# 查看容器内进程
docker [container] top 容器名

# 查看容器内日志 -t显示时间戳
docker [container] logs 容器名
docker [container] logs -tf 容器名 #类似与tail -f
```



#### 3. 容器关闭、启动与删除

```shell
docker [container] stop 容器名 # 停止容器运行
docker run -it centos --rm 镜像名 # 退出容器时自动删除该容器
docker rm 容器名 #删除容器

# 适用与守护式容器
docker start 容器名 # 启动容器 
# 适用与交互式容器
docker start -i 容器名 # 启动容器并连接终端 
```



#### 4. 连接容器(终端)

```shell
# 一个容器被多次attach连接时，各个attach的操作是同步的(都在同一个终端)
docker [container] attach 容器名 # 使用原有终端登录容器(退出终端容器停止)

# 使用子进程方式登录容器，退出终端仅结束当前子进程(/bin/bash)，容器仍然运行
docker [container] exec -it 容器名 /bin/bash # 使用新的的bash登录容器
```

#### 5. 端口映射

```shell
-P(大写) 容器端口 # 随机映射宿主机端口到容器指定端口
-p 宿主机端口:容器端口 # 宿主机的0.0.0.0(所有ip地址)的8080端口映射到容器80端口
-p 宿主机ip:宿主机端口:容器端口 # 宿主机指定ip端口映射到容器端口
-p 宿主端口:容器端口 -p 宿主端口:容器端口 # 指定多个映射
```



#### 6. 文件交互

```shell
# 将宿主机文件拷贝到容器内
docker container cp ./index.html nginx:/usr/share/nginx/html/
# 将容器内文件下载到宿主机
docker container cp nginx:/usr/share/nginx/html/index.html ./
```



#### 7. 数据卷(Volume) 文件共享&持久化

实现宿主机与容器的文件映射，取代docker cp

##### 使用目录作为数据卷

```shell
mkdir /opt/html; # 宿主机创建文件夹
# 创建容器时实现文件映射（挂载） 多个-v 挂载多个目录
docker container run --name nginx -d -p 8080:80 -v /opt/html:/usr/local/nginx/html nginx:latest

# 查看容器数据卷：inscept 命令的Mount节点

# 容器删除时不会删除数据卷，下次重新挂载依据可以使用
```

##### 使用数据卷容器作为数据卷

当多个容器需要挂载同一目录时，可以将这些目录挂载在一个数据卷容器上，其他容器只需挂载该数据卷容器即可

```shell
# nginx <--> 数据卷容器 <--> 宿主机

# 在宿主机创建所需数据卷目录
mkdir -p /opt/nginx/html;
mkdir -p /opt/nginx/conf;
# 生成数据卷容器
docker container run --name volume -dit -v /opt/nginx/html:/opt/nginx/html 
	-v opt/nginx/conf:/opt/nginx/conf centos
# nginx挂载数据卷容器
docker run --volumes-from volume --name nginx -d  -p 8080:80 nginx:latest
```



#### 8. 容器导入导出



### 五、镜像制作

#### 1. 基于容器制作镜像

步骤：

1. 基础镜像CentOS
2. 安装所需软件
3. 配置软件（如ssh需要设置key）
4. docker commit 讲容器保存为镜像

```shell
# 以及centos容器制作支持ssh服务的镜像
1. 启动centos容器
2. 安装ssh服务并启动
3. 将容器保存为镜像
# 制作镜像
# docker [image] commit -m '注释' 容器名 镜像名:标签
docker commit -m "" centos centSSH:v1

# 生成容器,默认启动后执行bash，ssh服务需要手动开启
# docker container run --name ssh -it centSSH:v1

# 指定要执行的命令,达到容器运行时启动sshd，但是该命令执行完成后就会退出
# docker container run --name ssh -it centSSH:v1 /usr/sbin/sshd

# 将sshd放在前台运行，避免命令执行完后退出容器
docker container run --name ssh -dit centSSH:v1 /usr/sbin/sshd -D
```



#### 2. DockerFile 制作镜像

相当于记录容器制作镜像的步骤，需要镜像时直接执行dockerfile生成镜像

步骤：

##### 编写dokerfile

```shell
1. cd /opt/dockerfile/ssh-server;
2. vim dockerfile
```



```dockerfile
FROM centos:latest # 使用centos作为基础容器
# FROM centos@镜像id # 安全性更高
RUN yum install -y openssh-server
# dockerfile每次RUN都会创建临时容器，尽量使用 && 将命令写在一行
# RUN 默认使用bash执行命令
# RUN ["命令","参数1","参数2",....] # 专用程序执行命令
RUN 配置sshserver
EXPOSE 22 # 对外暴露22号端口
# 启动镜像时执行命令
CMD ["命令","参数1",...]
```

##### 构建镜像

```shell
# cd到Dockerfile同级目录
docker build -t 镜像名:tag .
```





#### Docker集成多个服务

* dockerfile

```dockerfile

```

* 构建镜像

