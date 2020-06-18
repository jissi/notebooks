# NIO

### 1. nio简介

**new io , 可以理解为no blocking io，science jdk1.4 **

与传统IO （BIO）的目的和作用相同，但是使用方式不同

> NIO 支持面向缓冲区、基于通道的IO操作：缓冲区在通道内双向传输数据
>
> 传统IO面向流

### 2. nio与io的主要区别

| (B) IO                  | NIO                                  |
| ----------------------- | ------------------------------------ |
| 面向流(Stream Oriented) | 面向缓冲区（Buffer Oriented）        |
| 阻塞IO                  | 非阻塞IO （针对网络通信）            |
|                         | 选择器（Selectors)  （针对网络通信） |



### 3. 缓冲区（Buffer）与通道（Channel）

**Channel负责传输，Buffer负责存储**

* 通道：打开到IO设备( 文件，套接字)的连接
* 缓冲区：容纳数据并在通道内双向传输

#### 3.1 缓冲区

数组结构，负责数据存取，提供了 除Boolean以外的其他7种基本数据类型的缓冲数组：

ByteBuffer、ShortBuffer、CharBuffer、IntBuffer、LongBuffer、FloatBuffer、DoubleBuffer

```java
-1<= mark<=position<=limit<=capacity 
// 获取指定大小字节的缓冲区
ByteBuffer buff = ByteBuffer.allocate(1024);
// 存数据
buff.put( "abc".getBytes() );
// 切换读模式
buff.flip();
// 读取数据
byte[] b = new byte[2];
buff.get(b);
// 重新读取（将position和mark复位）
buff.rewind();
// 复位所有属性,但是数据依然存在
buff.clear();
// 记录当前position值，之后调用rewind会时mark失效
buff.mark();
// 恢复position值到记录点
buff.reset();
// 查看是否还有可读取的数据
buff.hasRemaining();//是否还有数据
buff.remaining();//数据数量

buff.isDerict()；//判断是否是直接缓冲区
```

#### 3.2 缓冲区属性

* 核心属性

  ```java
  capacity //缓冲区最大容量，一旦声明不能改变(数组容量)
  limit // 界限 可操作的数据大小，limit后不能操作
  position // 正在操作的数据的位置
      position <= limit <= capacity
  mark //标记当前position，调用rewind()后mark= -1
  ```




#### 3.3 直接缓冲区和非直接缓冲区

* 非直接缓冲区：通过allocate() 方法 在JVM内存中建立缓冲区

* 直接缓冲区：仅ByteBuffer支持，通过 allocateDirect() 方法 直接在物理内存建立缓冲区

  buff.isDerict()判断是否是直接缓冲区

  **直接缓冲区优缺点**

  > 避免了 数据在OS内核空间与JVM用户地址空间的来回拷贝，提高操作效率，
  >
  > 分配与销毁销毁大量资源，且不易控制（什么时候操作完成不可知，导致GC时间不可知，可能长时间占用资源）

### 4. 文件通道（FileChannel）

传统IO操作全部由CPU负责，后来有了DMA，向CPU申请后可以负责IO操作（IO流就是使用DMA），大量IO会到DMA总线冲突。

**通道：完全独立的处理器，专用于IO操作**

#### 4.1  java.nio.channels

* 建立源节点与目标节点的连接，本身不存储数据，需要配合缓冲区

* 主要实现类

  > java.nio.channels 接口
  >
  > > FileChannel  接口 操作本地文件
  > >
  > > SocketChannel、ServerSocketChannel  接口 TCP连接
  > >
  > > DatagramChannel 接口 UDP连接



#### 4.2 通道操作

##### 获取通道

```java
//---------------- 方法1 getChannel()
// 本地IO
FileInputStram/FileOutPutStream .getChannel();
RandomAccessFile.getChannel();
// 网络IO
Socket/ServerSocket/DatagramSocket .getChannel();
//-----------------方法2 open()
jdk1.7 中 nio2 针对通道提供 静态方法 open() 
FileChannel.open()
//-----------------方法3 Files.newByteChannel()
jdk1.7 中 nio2 工具类 Files 的 newByteChannel()
```

##### 通过IO流获取通道

```java
InputStream in = null;
OutputStream out = null;
FileChannel inChannel = null;
FileChannel outChannel = null;
try{
    //通过设置 in、out数据源
    // 通过in 、out getChannel
    // 设置缓冲区 buff
    //读写文件
    while (inchannel.read(buff) != -1){
        buff.flip();//切换写模式
        outChannel.write(buff);
        buff.clear();//“清空”缓冲区
    }
}catch (Exception e){
    e.printStackTrace();
}finally{
    try{
        //关闭通道
        //关闭流
    }catch(Exception e){
        ...
    }
}
```

##### 通过FileChannel.open()获取通道，并使用直接缓冲区

```java
// 获取通道
FileChannel inChannel = FileChannel.open(Paths.get("file.txt"), StandardOpenOption.READ);
FileChannel outChannel = FileChannel.open(Paths.get("file1.txt"),
                                          StandardOpenOption.WRITE,
                                          StandardOpenOption.READ,
                                          StandardOpenOption.CREATE_NEW);// StandardOpenOption.CREATE 
// 使用直接缓冲区（内存映射文件/缓冲区）
MappedByteBuffer inMapBuf = inChannel.map(FileChannel.MapMode.READ_ONLY, 0, inChannel.size());
MappedByteBuffer outMapBuf = outChannel.map(FileChannel.MapMode.READ_WRITE, 0, inChannel.size());
// 操作缓冲区
byte[] dst = new byte[inMapBuf.limit()];
inMapBuf.get(dst);
outMapBuf.put(dst);
//关闭通道
inChannel.close();
outChannel.close();
```



##### 通道间直接传输数据（使用直接缓冲区）(代码最少)

```java
FileChannel inChannel = FileChannel.open(Paths.get("callPic1.jpg"), 
                                         StandardOpenOption.READ);
FileChannel outChannel = FileChannel.open(Paths.get("2.jpg"), 
                                          StandardOpenOption.READ, StandardOpenOption.WRITE, StandardOpenOption.CREATE);
// 使用直接缓冲区进行传输
inChannel.transferTo(0,inChannel.size(),outChannel);
//outChannel.transferFrom(inChannel,0,inChannel.size()); 与上面等效
inChannel.close();
outChannel.close();
```



#### 4.3 分散（Scatter）与聚集（Gather）

* 分散读取（Scatter Read）：将通道中的数据按序分散到多个缓冲区
* 聚集写入（Cather Write）：将多个缓冲区的数据按序聚集到通道中

```java
RandomAccessFile scatter = new RandomAccessFile("file.txt","rw");
FileChannel inChannel = scatter.getChannel();
//设置多个缓冲区
ByteBuffer buff1 = ByteBuffer.allocate(20);
ByteBuffer buff2 = ByteBuffer.allocate(1024);
ByteBuffer[] buffers = {buff1,buff2};
//分散读
inChannel.read(buffers);
// 聚集写
RandomAccessFile gather = new RandomAccessFile("file3.txt","rw");
FileChannel outChannel = gather.getChannel();
// 缓冲区切换为读模式
for (ByteBuffer buffer:buffers){
    buffer.flip();
}
outChannel.write(buffers);
outChannel.close();
inChannel.close();
```

#### 4. 4 字符集 Charset

> 编码：字符串 -> 字节数组
>
> 解码：字节数组 -> 字符串

* 查看Java支持的所有编码  Map<String,Charset> map = Charset.availableCharsets()

  ```java
  //获取编码方式
  Charset c1 = Charset.forName("UTF-8");
  CharBuffer charBuffer = CharBuffer.allocate(1024);
  charBuffer.put("使用utf-8编码方式");
  charBuffer.flip();
  // 编码为字节数组
  ByteBuffer buffer = c1.encode(charBuffer);
  // 查看编码后的值
  for (int i=0;i<buffer.limit();i++){
      System.out.println(buffer.get(i));
  }
  //解码为字符数组
  CharBuffer charBuffer1 = c1.decode(buffer);
  for (int i=0;i<charBuffer1.limit();i++){
      System.out.println(charBuffer1.get(i));
  }
  ```

  

### 5. NIO非阻塞式网络通信

java.nio.channels接口

> SeletableChannel
>
> > SocketChannel
> >
> > ServerSocketChannel
> >
> > DatagramChannel
> >
> > Pipe.SinkChannel
> >
> > Pipe.SourceChannel

#### 5.1 选择器（Selector）

用于监控SelectableChannel的IO状况

```java
Selector selector = Selector.open();//开启选择器
// 将通道注册到选择器（通过必须是非阻塞模式）,并指定监听该通道的某状态
ServerSocketChannel ssc = ServerSocketChanel.open();
ssc.configureBloking(false);//使用非阻塞
ssc.bind(new InetSocketAddress(8080))；
ssc.register(selector,SelectionKey.OP_ACCEPT);//监听该通道的接收

// 监听四种状态
/**
* SelectionKey.OP_ACCEPT\CONNECT\READ\WRITE
* 
*/
// 轮询通道状态
while(selector.select() > 0){//获取选择器中的所有已经就绪的状态(accept\connect\read\write)
	// 获取迭代器
	... iter = selector.selectKeys.iterator();
    while(iter.hasNext()){
    	... k = iter.next();
        if(k.isAcceptable()){//该key接收就绪(可接收)，接收客户端并注册
        	ServerSocket cch = ssc.accept();
            cch.configureBloking(false);// !!
            cch.register(selector, xxREAD);//注册，并监听她的read就绪
         }else if(k.isReadable()){//该key可读取，获取该key对应的通道，并读取数据
            .. sc =(SocketChannel)k.channel();
            ByteBuffer buf = ByteBuffer.allocate(1024);
            	// 读取数据....
         }else if(...){}
         ...
         iter.remove();//！！ 移除当前状态
    }
}
```



#### 5.2 SocketChannel、ServerSocketChannel、DatagramChannel

* NIO 实现阻塞式网络通信：必须调用shutdown，否则一直阻塞

  ```java
  // ----------------------client
  SocketChannel socketChannel = SocketChannel.open(new InetSocketAddress("127.0.0.1", 8090));
  ByteBuffer buffer = ByteBuffer.allocate(1024);
  FileChannel fileChannel = FileChannel.open(Paths.get("file.txt"), StandardOpenOption.READ);
  while (fileChannel.read(buffer) != -1){
      buffer.flip();
      socketChannel.write(buffer);
      buffer.clear();
  }
  socketChannel.shutdownInput();//告知服务端发送完毕，否则服务端一直等待数据
  fileChannel.close();
  socketChannel.close();
  // ----------------------server
  ServerSocketChannel serverSocketChannel = ServerSocketChannel.open();
  serverSocketChannel.bind(new InetSocketAddress(8090));
  SocketChannel accept = serverSocketChannel.accept();
  ByteBuffer buffer = ByteBuffer.allocate(1024);
  FileChannel fileChannel = FileChannel.open(Paths.get("fileSocket.txt"),
                                             StandardOpenOption.WRITE, StandardOpenOption.CREATE);
  while (accept.read(buffer) != -1){
      buffer.flip();
      fileChannel.write(buffer);
      buffer.flip();
  }
  accept.shutdownOutput();//告知客户端接收完毕
  fileChannel.close();
  accept.close();
  serverSocketChannel.close();
  ```

* NIO 非阻塞式网络通信

  ```java
  间 Selector
  ```

* NIO 实现UDP通信

  ```java
  // 发送方
  DatagramChannel dc = DatagramChannel.open();
  dc.configureBloking(false);
  ByteBuffer buffer = ByteBuffer.allocate(1024);
  buffer.put("124".getBytes());
  buffer.flip();
  dc.send(buffer,new InetSocketAddress("127.0.0.1",8090));
  buffer.clear();
  // 接收方
  DatagramChannel sdc = DatagramChannel.open();
  dc.configurBloking(false);
  dc.bind(new InetSocketAddress(8090));
  Selector sel = Selector.open();
  dc.register(sel,SelectionKey.OP_READ);//udp无连接，直接监听读是否就绪
  //轮询
  while(sel.select() > 0){
      Iterator<..> i =  sel.selectKeys.iterator;
      while(i.hasNext()){
          if(i.isReadable()){
              ByteBuffer buf = ....;
              dc.receive(buf);
              ...
          }
          i.remove();
      }
  }
  ```

  

### 6. 管道（Pipe）

单向传输：

```java
Pipe pipe = Pipe.open();
Thread t1 = new Thread(()->{
    ByteBuffer buf = ByteBuffer.allocate(1024);
    buf.put("test".getBytes());
    buf.flip();
    Pipe.SinkChannel sinkChannel = pipe.sink();
    try {
        sinkChannel.write(buf);
    } catch (IOException e) {
        e.printStackTrace();
    }
});
t1.join();t1.start();
ByteBuffer buf = ByteBuffer.allocate(1024);
Pipe.SourceChannel sourceChannel = pipe.source();
sourceChannel.read(buf);
buf.flip();
System.out.println(new String(buf.array(),0,buf.limit()));
}
```

