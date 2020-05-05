<h1>JavaIO</h1>
<center>Contents</center>
[TOC]

---

### 核心类

| 类           | 功能                                     |      |
| ------------ | ---------------------------------------- | ---- |
| File         | 文件类                                   |      |
| InputStream  | 字节输入流                               |      |
| OutputStream | 字节输出流                               |      |
| Reader       | 字符输入流                               |      |
| Writer       | 字符输出流                               |      |
| Closeable    | 管理流接口：通知OS可以释放资源  close()  |      |
| Flushable    | 刷新流接口：强制将流中的数据输出 flush() |      |
| Serializable | 序列化接口：存储对象                     |      |

### 1. File（对文件系统的描述）

File并不是某个存在的文件，而是一种抽象概率，是对文件系统上某个文件的描述

这个文件可能存在也可能不存在；

<font color="red">JVM无权直接操作硬盘文件，所以需要借助操作系统</font>

file常用方法：

>构造：File("文件路径")、File(file,"文件路径")、...
>
>判断：exists()、isFile()、isDirecotry()
>
>创建：createFile()、mkdir()、mkdirs()
>
>获取子文件：list()、listFiles()

### 2. 编码与解码

编码（encode）：字符 -> 字节

解码（decode）：字节 -> 字符

常用字符集：UTF-8 变长字符集（英文1字节，中文3字节）

相关方法：

> str.getBytes()
>
> new String(byte数组,"Utf-8")

乱码的原因：解码方式与编码不一致；字节不够（utf8中中文必须有3个字节）

### 3. IO流分类及设计模式

* 节点流与处理流

  

  节点流处于IO操作的第一线，所有操作必须通过他们进行；

  处理流可以对其他流进行处理:<font color="green">处理流（包装流----装饰器设计模式）</font>

  ![](../resources/img/io-stream.png)

  

* 输入输出流

  ```shell
  以程序为中心
  输入流---->程序-----> 输出流
  InputStream、Reader
  OutputStream、Writer
  ```

* 字符流字节流

  文本多采用字符流、音视频等多采用字节流

### 4.四大抽象类

| 抽象类       | 说明             | 常用方法                                    |
| ------------ | ---------------- | ------------------------------------------- |
| InputStream  | 字节输入流的父类 | int read()、void close()                    |
| OutputStream | 字节输出流的父类 | void write()、void flush()、void close()    |
| Reader       | 字符输入流的父类 | int read()、void close()                    |
| writer       | 字符输出流的父类 | void write(str)、void flush()、void close() |



### 5.IO流标准操作

```shell
创建源 - 选择流 - 操作 - 释放系统资源
```

```java
public class StranderIO {
    public static void main(String[] args) {
        //创建源
        File file = new File("G:/data/Idea_workSpace/JavaSe/file.properties");
        InputStream inputStream = null;
        try{
            //选择流
            inputStream = new FileInputStream(file);
            //操作
            int flag;
            while ((flag=inputStream.read()) != -1){
                System.out.println((char) flag);
            }
        }catch (FileNotFoundException e1){
            e1.getMessage();
        }catch (IOException e2){
            e2.getMessage();
        }finally {
            //通知系统释放资源
            try {
                if (null != inputStream) inputStream.close();
            } catch (IOException e) { e.printStackTrace(); }
        }
    }
}




老版本自动释放资源
try(
    InputStream in = new FileInputStream(file);
    OutputStrea out = new OutputStream(file)
   ){...}catch(...){}
```

##### 先打开的流后关闭

### 6.节点流：借助操作系统操作硬盘文件（需要手动通知操作系统释放资源）

```FileInputStream 、FileOutputStream；```

```FileReader、FileWriter；```

### 7. 节点流：字节数组流：操作内存，不借助操作系统无需关闭

任何数据都可以转为字节数组，方便在网络上传输

```ByteArrayInputStream```

```ByteArrayOutputStream 不需要指定目的地，通过toByteArray()、toString()拿到数据```

<font color="red">ByteArrayOutputStream自己新增了方法 ，使用新增方法是不能使用多态 ，必须 ByteArrayOutputStream out = new ByteArrayOutputStream()</font>

```shell
1. 数据源由内部维护
2. 选择流不用关联源
3. 释放资源不用close

获取数据(从内存)：toByteArray()、toString()

使用字节数组流来实现中转
文件字节输入流 --> 程序 --> 字节数组输出流
字节数组输入流 --> 程序 --> 文件字节输出流
```

### 8.装饰器设计模式

```shell
# 抽象组件：InputStream
# 具体组件：FileInputStream、ByteArrayInputStream
# 抽象装饰者：FilterInputStream
# 具体装饰类：BufferedInputStream、DataInputStream、ObjectImputStream
```

为了达到某一目标组织代码的固定套路

方便后期操作

```java
/**
 * 1. 抽象组件：需要装饰的抽象对象（接口或抽象父类）
 * 2. 具体组件：需要装饰的对象
 * 3. 抽象装饰类：包含了对抽象组件的引用以及装饰着共有的方法
 * 4. 具体装饰类：被装饰的对象
 */
//示例

//抽象组件
interface Drink{double cost();String info();}
//具体组件
class Coffee implements Drink{
    @Override
    public double cost() {return 10;}
    @Override
    public String info() {return "原味咖啡"; }
}
//抽象装饰类
abstract class Decoreter implements Drink{
    //对抽象组件的引用
    private Drink drink;
    Decoreter(Drink drink){this.drink = drink;}
    @Override
    public double cost() {return this.drink.cost(); }
    @Override
    public String info() {return this.drink.info(); }
}
//具体装饰类1
class Milk extends Decoreter{
    Milk(Drink drink){ super(drink);}
    @Override
    public String info() {return super.info()+"加入牛奶"; }
    @Override
    public double cost() {return super.cost()+5;}
}
//具体装饰类2
class Suger extends Decoreter{
    Suger(Drink drink){super(drink); }
    @Override
    public String info() {return super.info()+"加入蔗糖"; }
    @Override
    public double cost() {return super.cost()+2; }
}


//使用装饰器
// new 具体装饰类(new 具体组件).接口方法()
Drink coffee = new Coffee();
System.out.println(coffee.info()+">"+coffee.cost());

Drink suger = new Suger(coffee);
System.out.println(suger.info()+">"+suger.cost());

Drink milk = new Milk(coffee);
System.out.println(milk.info()+">"+milk.cost());

Drink sugerMilk = new Milk(suger);
System.out.println(sugerMilk.info()+">"+sugerMilk.cost());
```

### 9.处理流（装饰流）

* 字节缓冲流：BufferedInput/outputStream：提高操作性能

  1. 提示性能
  2. 底层一定是节点流
  3. 释放时只需要释放外层处理流（节点流会自动释放），如果手动释放需要先释放节点流

  <font color="red">操作字节流时最好都使用缓冲流来优化性能</font>

*  字符缓冲流：BufferedReader/Writer：有新增方法，注意使用新方法是不能多态

  reader.readLine() ;writer.newLine()

*  转换流：InputStreamReader/OutputStreamWriter ：字节流与字符流直接的桥梁

  1. 将字节流转为字符流（前提是字节流是纯文本）

     InputStreamReader可以指定解码

     OutputStreamWriter可以指定编码

  2. 为字节流指定字符集

* 数据处理流：DataInputStream & DataOutputStream

  1. 能够处理基本类型和String类型

  2. 先写出，读取时按写出的顺序读取

     ```java
     //有新增方法 
     writeUTF(String str);...writeInt()....
     readUTF()....
     ```

* 对象处理流：ObjectInputStream & ObjectOutputStream  

  写出对象（持久化/序列化）、读入对象(反序列化)

  1. 能够将对象写入文件
  2. 操作的对象必须是可以序列化的 <font color="red">实现Java.io.Serializable接口</font>

* 字节打印流：PrintStream 只有输出作用

  System.out 就是一种打印流：PrintStream ps = System.out

* 字符打印流：PringWriter

* 随机流：RandomAccessFile

  ```java
  RandomAccessFile f = new RandomAccessFile("name","rw")；
  //既可以读又可以写 模式:r rw
  
  seek(long 指针位置)；//从指定位置开始读或写
  ```

```java
        RandomAccessFile file = new RandomAccessFile(path,"r");
        //设置指针起始位置
        file.seek(2);
        //每次接收长度
        byte[] flush = new byte[1024];
        int len;
        while ((len = file.read(flush)) != -1){
            System.out.println(new String(flush,0,len));
        }
        file.close();
```

### 10.CommonsIO

* FileUtils

  ```java
  FileUtils.SizeOf(file);//文件大小
  读：read...;
  写：write..;
  
  copyFile();//拷贝操作
  copyDirectoryToDirectory();//拷贝到子目录
  copyDirectory();//只拷贝文件
  ```

  