### String类(public final Class String)

* 字符串，用双引号

  ```java
  String s = "abc" 
  <==> String s = new String("abc")//String常量也是对象，注意引用相同才相同
  <==> 
  char c[] = {'a','b','c'}；
  String s = new String(c[])
  ```

* String对象输出时会输出实体而不是引用,可以用String对象创建String对象

* <font color="red">常用构造方法</font>

  ```java
  String(char a[])、（int a[]）	//使用字符\其他数组创建字符串
  String(char a[],int startIndex,int count)	//选取部分字符创建字符串
  String(char a[],int offset,int length)		//选取指定位置长度的字符数组构成字符串
  ```

* 字符串的并置运算

  使用+使多个String对象连接构成新的String对象

* <font color="red">String类常用方法</font>

  ```java
  public int length()	//获取String对象长度
  public boolean equals(String s)  //两String对象的实体的值是否相等
  public boolean startsWith(String s) //是否以s开头
  public boolean endsWith(String s)   //是否以s结尾
  public int compareTo(String s) 、compareToIgnoreCase()忽略大小写
      //比较两String对象的字符排列顺序，(各字符Unicode编码),
      //等于为0，大于为正，小于为负
  public boolean contains(String s) //是否包含s
  public int indexOf(String s)、lastIndexOf()
      //返回s首次出现或最后一次出现的索引，不存在则返回-1
  public int indexOf(String s,int startpoint) 
      //从某位置开始检索s
  public String substring(int startpoint)、(int start,int end)
      //获取指定位置的字符串
  public String trim()  //获取去掉空格后的字符串
  ```

  

* <font color="red">字符串与基本数据的相互转化</font>

  Integer类的下列方法将String转为int

   ```public static int parseInt(String s) throws NumberFormException```

  tips：建议使用 ```Integer.parseInt(s.trim())```字符串中由空格会异常

  其他类型同理

  String类的下列方法将其他类型转为String

  ```public static String valueOf(int n)``` ```、(float n)...```

* 对象的字符串表示

  Object类的方法 public String toString（）获得一个对象的字符串表示

  Date类重写了toString方法，获取的是时间序列

  Arrays.toString(char a[]) 	将数组转为字符串

* <font color="red">字符串与字符数组，字节数组</font>

  ```java
  public void getChars(int start,int end,char c[],int offset)
      //将字符串存到数组，offset：从数组的offset位置开始存放，
      //注意数组容量
  public char[] toCharArray()	//string对象调用该方法返回一个数组，全部字符串转为数组，返回数组引用
  
  public byte[] getBytes(String 字符编码方式) throws UnsuppprtedEncodingException	
   //string对象调用该方法，将字符按编码方式所占字节，存到字节数组，并返回数组引用
  ```

* 正则表达式

  ```java
  String对象调用
  public boolean matches(String regex) //判断是否匹配
  public String replaceAll(String re,String replacement)//替换字符
  public String[] split(String re)//以re为分隔符，将分解出的字符放入数组
  	//分隔符的左侧应该是单词，如果没有，则为空字符 “”
  	//注意与python中split的区别
  ```

  

### StringTokenizer类(java.util.StringTokenizer)

​	stringTokenize类可以将String对象分割为单个单词（语言符号）

​	常用构造方法构造字符串分析器

```java
StringTokenizer(String s)  //为字符串构造分析器
    //默认分隔符：空格，换行，回车，tab,进制符
StringTokenizer(String s,String delim) //指定分隔符
```

方法

```java
nextToken() //返回语言符号
hasMoreTokens() //只有还有语言符号就为true
countTokens()   //获取分析器中的计数变量的值(计数变量初始值为语言符号总数，执行一次nextToken()该变量就-1)
```

<font color="green">处理用户输入数据时，一般先使用String对象.replaceAll()方法，将不必要的数据转换成分隔符，在使用StringTokenizer(String s,String delim)构造方法将替换后的String对象分割</font>



### Scanner 类（java.util.Scanner)

Scanner也可用于分解字符

scanner类没有把单词存入到实体，占用内存小，但速度比StringTokenizer慢

StringTokenizer对象一诞生就知道语言字符数量，而Scanner对象需要逐个访问计数

```java
Scanner(String s)	//构造Scanner对象
Scanner(File file)//指定要解析的文件，默认空格拆分
userDelimiter(String re)	//将re作为分隔符，默认参数：空格等
    与StringTokenizer(String s,String delim)类似
Scanner对象调用 next()  //返回被解析的单词
    boolean hasNext() //是否还有单词
    int nextInt()throws InputMismatchException //返回int型单词，替代next()
    double nextDouble() ...                    //同上
    
```



### StringBuffer类

​	String对象的字符序列不能修改（String实体不能变化）

​	StringBuffer对象的实体内存能够自动变换大小，用于可变字符序列

```java
构造方法
	StringBuffer()	//基础容量为16字节，可自动增大
	StringBuffer(int size)	//指定基础容量size，自动增大
	StringBuffer(String s)	//基础容量为string长度+16字节
常用方法
	StringBuffer append(String s) //将s追加到序列，并返回当前对象引用
	StringBuffer append(int n)	  //将n转为int型追加到序列，并返回当前引用
	类似的 参数可以是其他数据类型，返回当前对象引用
	public char charAt(int n)	  //返回位于n索引位置的字符
    public void setCharAt(int n,char ch)//用ch替换n索引位置的字符
    StringBuffer insert(int index,String n)//在index处插入s，原index位置及其后的值全部后移
    public StringBuffer reverse()  //反向并返回引用
    StringBuffer delete(int startIndex,int endIndex)//删除一段，并返回引用
    					(int index)//删除指定位置，并返回引用
    StringBuffer replace(int startIndex,int endIndex,String s)
    			//用s替换startIndex～endIndex的整段字符
```



### Date类和Calendar类

* Date类构造方法

  ```java
  Date()	//该对象获取本机时间
  Date(long time) //time单位为毫秒，该对象时间为1970年1月1日0时+time
      
  方法
  System.currentTimeMillis()//获取当前时间系统走过的毫秒数（距离1970年。。。）
  ```

* Calendar类

  ```java
  使用静态方法初始化日期对象
  	Calendar calendar = Calendar.getInstance()
  设置日期
  	public final void set(int year,int month,int date)
      		(int year,int month,int date,int hour,int minute)
      	(int year,int month,int date,int hour,int minute,int second)
     	public int get(int field)//获取指定类型时间
      	//field：Calendar.MONTH	0为一月
      	//Calendar.DAY_OF_WEEK	 0为周日
      public long getTimeMillis()//返回当前时间距离计算机元年的毫秒
      setTime(Date date);
  ```

  

### 日期格式化

String.format(“格式化模式”，日期列表（Date对象或Calendar对象）)

格式化模式：%tY表示四位的年, %tm为数值月，其他类推

每一种格式化字符对应一个格式的对象，要解析年月日，就要对应3个对象

String.format(“%tY -  <%tm - <%td”,new Date())中使用<表示使用上一个格式使用的日期对象即，new Date()  

String.format(Locale locale,格式化模式,日期列表)	//java.util.Locale的static常量都是对象，按时区格式格式化

### 数据格式化

String.format(“格式化模式”，数值列表)

参数与printf参数类似

* 指定格式化第几个数字  %3$f   指定第三个数值

* 格式化整数

  %d，%o，%x，%X：10,8，小写16、大写16进制

  %+d 强制加上+号

  %，d 按千分组

  %3d 左边加3个空格

  %-3d 右边加3个空格

  %08d  左边加8个0

### Math类

```java
用来计算的static方法
两个static常量 E  和  PI
random() //产生[0,1）之间的随机数
pow()	//幂运算
sqrt()	//平方根
log()	//返回a的对数
sin()	...
ceil(a)	//返回大于a的最小整数
floor(a)//返回小于a的最大整数
round(a)//返回a的四舍五入值 <==> Math.floor(a+0.5)
    //a为非负数，>= 0.5 入
    //a为负数,  > 0.5入
```

### BigInteger类

```java
构造方法，构造非常大的整数对象
BigInteger( String val) throws NumberFormatException
	//val为字符型数字，含有非数值型会抛出异常
方法
public BigInteger add(BigInteger val)//与val求和
				  subtract()
    			  multiply() ...
public String toString() //返回十进制字符串
public String toString(int p)//p为进制
```

### Random类

```java
构造方法
Random()  //使用系统时间作为随机种子,构造对象
Random(long seed)//指定随机种子
方法 Random random = new Random（）
random.nextInt()//返回随机整数
random.nextInt(int m)//返回0～m的整数
random.nextDouble()
    nextboolean()...
```

### Class类

使用Class类创建对象实例，常用的是   new 构造方法

```java
public static Class forName(String 类名) throws ClassNotFoundException
	//返回指定类相关的Class对象，当对象在包中是要带有包名如：java.util.Date
public Object newInstance() throws InstantiationException,IllegalAccessException
	//Class对象调用该方法返回一个实例对象（该类必须要有无参数的构造方法）
```

eg.

```java
class A{ public A(){} } 	//必须有无参数的构造方法
Class a = Class.forName("A");
A b = (A)a.newInstance();//b即为class A的对象
```

### Console类

```java
Console console = System.console();//System类调用console方法返回Console对象
char a[] = console.readPasswd();//读取用户输入，不会显示
```

### Pattern类Matcher类java.util.regex

用于模式匹配

```java
Pattern pattern = Pattern.complie("regex")//得到Pattern对象
Matcher matcher = pattern.matcher("String")//用re匹配字符串，得到Matcher对象 
```





