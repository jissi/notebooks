### jenkins安装配置

* 下载jenkins war包

  * 下载war包，默认保存在 /usr/lib/jenkins/jenkins.war

  * 启动jenkins并指定端口号

    ```shell
    nohup java -jar jenkins.war --httpPort=9000 &
    ```

* 配置jenkins
  * 打开浏览器访问9000端口，从提示路径中复制密码并提交
  * 选择推荐配置