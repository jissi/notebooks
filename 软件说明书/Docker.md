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



#### 使用步骤：

1. 安装Docker
2. 去Docker仓库找打软件镜像
3. 使用Docker运行镜像，生成容器
4. 容器的启动和停止就是对软件的启动和停止



### 二、Docker安装和使用

* docker命令

  ```shell
  docker -v 查看dokcer版本
  systemctl start docker 启动docker
  ```

* 镜像操作

  镜像站：https://hub.docker.com/

  ```shell
  使用docker镜像加速：www.docker-cn.com
  docker search mysql #搜索镜像
  docker pull mysql [:5.5] #拉去镜像[:标签|默认latest]
  docker images #查看镜像
  docker rmi IMAGE ID #删除镜像（根据镜像id）
  ```

* 容器操作

  一个镜像可以启动多个容器

  ```shell
  docker run --name 容器取名 -d mysqld:5.5 #启动镜像，生成容器，-d 后台运行
  docker [--name xxxx] -d -p 8888:8080 tomcat #-p 端口映射 主机端口8888映射到容器端口
  docker ps #查看启动的容器
  docker ps -a #查看所有容器
  docker stop CONTAINER ID/容器名 #停止容器
  docker start CONTAINER ID/容器名 #启动容器
  docker rm CONTAINER ID/容器名 #删除容器
  docker logs CONTAINR ID/容器名 #查看容器日志
  ```



### 三、docker安装mysql

* mysql容器创建需要参数

  ```shell
  docker run -p 3306:3306 --name mysql_1 -e MYSQL_ROOT_PASSWORD=root -e TZ=Asia/Shanghai -d mysql
  # 检查mysql
   docker exec -it mysql_1 bash #进入容器终端
    mysql -uroot -p
  ```

### 四、docker安装tomcat

```shell
docker run -p 8080:8080 --name mytomcat tomcat
docker exec -it mytomcat bash #进入tomcat安装目录


docker cp demo.war mytomcat:/usr/local/tomcat/webapps # 项目部署到tomcat
```

