### java网络编程

!!! BIO网络通信需要调用 socket.shutdownInput()才能告知服务端发送完毕，否则服务端一直等待客户端发送数据

java.net

网络上的一切都是流

关键词：socket 、ServerSocket、DatagramPacket、DatagramSocket、URL

##### TCP/IP四层  ：数据链路层 - 网络层 - 传输层 - 应用层

* TCP/IP ：传输控制协议/网络互连协议 

  三次握手，四次挥手，分为客户端和服务端，传输完成释放连接

  打电话-有连接，稳定

* UDP ：用户数据报

  发短信-无连接，不稳定，传输快

  直接发送，对方是否收到不确定



---

##### URL类

使用URL类创建对象的程序称为客户端

URL对象包含：协议，地址、端口和资源

```xml
http:// localhost:8080 /[项目名] /资源
```



```java
构造方法,MalformedURLException
URL url = new URL("http://www.baidu.com")
URL url = new URL("协议","主机ip或域名","资源")
```

读取URL中的资源

```java
InputStream in = url.openStream();//返回输入流，指向url对象所包含的资源
//或者
URLConnection urlConnection = url.openConnection();
urlConnection.getInputStream()
```

URL资源的读取因为网速等原因会阻塞，需要单独的线程

* InetAddress类

  InetAddress对象含有主机域名和ip

  ```java
  try{
      InetAddress name = InetAddress.getByName(String s)//获取域名或ip参数s的域名和ip
      name.toString();
  }
  catch(UnknownHostException){}
  InetAddress local = InetAddress.getLocalHost()//获取本机域名和ip
  ```

  ```java
  InetAddress实例方法
  	public String getHostName()//获取域名
  	public String getHostAddress()//获取域名IP
  ```

  

* 套接字：Socket类

  IPAddr+port

  0~1023端口有固定功能

  普通程序使用1024~65535端口

  客户端套接字Socket + 服务端套接字ServerSocket

  ---

  

#####  客户端ServerSocket

构造方法

```java
Socket client = new Socket(String ipAddr,int port)//  IOException
    //一次性完成连接
Socket s = new Socket()
s.connect(SocketAdddress 对象) // IOException,连接到与SocketAddress对应的服务器
使用SocketAddress的子类 InetSocketAddress构造对象
InetSocketAddress addr = new InetSockerAddress(InetAddress addr,int port) 
  
```

实例方法

```java
InputStream in = client.getInputStream()//获取来自服务端的输入流
client.shutdownInputStrem();//通知服务端接受完毕
OutPutStream out = client.getOutputStream()//获取自己输出到服务器的流
client.shutdownOutputStream();//通知服务端发送完毕
```

##### 服务器端ServerSocket

构造方法

```java
ServerSocket server = new ServerSocket(int port)// IOException
```

实例方法

```java
server.setSoTimeout(int timeout)//设置等待时长 SocketTimeoutException
Socket sc = server.accept()//返回一个和客户端相连接的Socket对象；接收客户端的socket对象
InputStream in = sc.getInputStream()//获取客户端的输入
sc.shutdownInputStream();//通知客户端接受完毕
OutputSream Out = sc.getOutputStream()//输出到客户端
sc.shutdownOutputStream();//通知客户端发送完毕
InetAddress addr = sc.getInetAddress()//获取含有客户端域名ip的InetAddress
sc.close()//关闭通信
```

<font color="red">读取文件数据与读取Socket数据的区别：</font>

accept方法会阻塞线程，直到收到客户端的输入，等待数据；文件读取时，数据已经存在

##### 使用多线程

可能在接收到数据前就开始读取，造成线程阻塞直到读取到数据，然后才能执行后续操作。

```java
Socket socket = new Socket();
InetAddress addr = InetAddress.getByName("www.baidu.com");
socket.connect(addr,2020);
```

* 服务器应该专门启动一个线程用来连接客户端套接字
* 客户端与服务器需要在单独线程读取信息



##### tomcat

服务端程序，默认端口8080，接收浏览器发来的请求

---

##### UDP数据报

无法得知是否正确到达，无连接传输，速度比tcp快

* 数据包：将数据打包发送
* 拆包：接受并读取数据包内容

##### DatagramPacket类：创建数据包对象，封装目的地和数据

构造方法：

```java
DatagramPacket(byte[] 字节数据数组,InetAddress 目标地址,int 端口号)
    //打包数据，用于发送，数据包长度不要超过8192K 
DatagramPacket(byte[] b,int offset,,int lengthInetAddress addr,int port)
    //打包数据，用于发送
DatagramPacket(byte[] data,int length) //用于接受数据包到data
```

实例方法：

```java
int getPort() //返回包的目的地端口号
InetAddress getAddress()//返回目的ip地址
byte[] getData() //返回数据包的字节数据数组
int getLength()//返回数据长度
```

##### 收发数据包：

```java
发送数据包：
DatagramSocket socket = new DatagramSocket();//创建发送包的对象，封装数据和地址
socket.send(datagrampacket);//发送数据包

接受数据包：
DatagramSocket receive = new DatagramSocket(int port);//创建接受对象，监听端口
DatagramPacket pack = new DatagramPacket(byte[] b,int length)
receive.receive(DatagramPacket pack)//接受数据包到pack
    //receive方法阻塞，直到收到数据包
```



##### 在线聊天

多线程udp，一个线程发送，一个线程接收