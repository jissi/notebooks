### Chapter 1-3

#### 数据类型

| byte   | short  | char   | int    | float  | long   | double |
| ------ | ------ | ------ | ------ | ------ | ------ | ------ |
| 1 byte | 2 byte | 2 byte | 4 byte | 4 byte | 8 byte | 8byte  |
| 0-2^8  | 0-2^16 |        | 0-2^32 |        | 0-2^64 |        |
|        |        |        |        |        |        |        |

main方法中的参数 String []args 是一个String型的数组，一般不使用

#### 引用型数据：

​	数组，对象，接口等使用引用的数据

---

#### 输入、输出

```java
java.util.Scanner
Scanner reader = new Scanner(System.in)
读取输入方法：nextInt()、nextShort() nextLine()...

System.out.print()
System.out.println() //换行输出
转义符号   \' 、\b 、\n

并置  +
常量+变量+表达式+"字符（串）"

System.out.printf("格式语句",表达式1,表达式2,...)
	%d	输出int
	%c	输出char
	%f	输出float 保留小数点后6位
	%s	输出string 
	%md	输出int 占m列
	%m.nf	输出float 占m列，小数点保留n位
```

---

#### 数组

* 声明数组

```java
	int ls[]	、int ls[][]
	int [] ls	、int [][] ls
	char [] a,b;/char a[],b[];声明多个数组
	
	char [] a,b[];<==> char a[],b[][];
```

<font color="red">声明数组时方括号内不能指定数组长度</font>

* 给数组分配元素byte

  ```
  已声明的数组名 = new 数组类型[元素个数]
  ls  = new int[12];//创建数组后，系统为其分配默认值
  指定元素
  ls[0] = 0;
  ls[1] = 1; <==> int ls[] =  {0,1}
   
  ```

<font color="red">声明时即定义长度：int ls[] = new int[10]</font>

* 直接输出数组会输出数组的引用值而不是元素；char型数组会输出元素，‘’‘’+ls 即可输出引用，使用Arrays.toString(double a[]) 可以将数值数组元素输出

#### switch 语句

* case语句后不接break时会继续执行后续case；

#### for循环

* <font color='red'>增强for循环：for （声明变量：数组）</font>

#### 幂运算

* Math.pow(double a,double b)    

#### 运算与赋值

* 运算顺序 ： 

  ​		算术运算>关系运算>逻辑运算

* 运算精度

  运算中有double型时，运算精度为double

  运算中最高精度是float时，运算精度为float

  最低精度为int。



---



### Chapter 4

* 面向对象的特性

  封装：数据和对数据的操作封装到一起；eg：class

  继承：子类继承父类的属性和行为，又可以添加子类独有数据和操作

  多态：多个操作具有相同的名字，到各个所接收的参数不同；不同对象调用操作产生不同行为。两种多态：重载、重写

  

* class

  * 类名：

    class A{   }	各个首字母大写

  * 类体：

    * 声明变量

      成员/域变量：class内有效，与声明位置无关，有默认值

    * 定义方法

      方法头：int A( ){   }

      ​			访问权限 方法类型 方法名 方法参数

      ​			方法类型是void时，不需要return

      方法体：

      ​			局部变量：在方法体中声明，声明后语句生效，在方法内有效，无默认值（需赋值）
    
      
    
      *<font color ="red">参数</font>*：
    
      ​	调用方法时的参数类型不能高于参数预定义类型（eg，不能向int参数传入float值）
    
      *<font color="red">可变参数</font>*：
    
      ​	可变参数写在参数最后；
    
      ​	public void f ( int ...x )	; x 为参数列表中的可变参数代表
    
      ​	可变参代表[ 0 ]	;  访问某个参数
    
      ​	for （声明变量：可变参代表）；访问可变参数列表
    
      ​	

    <font color="red">局部变量与成员变量重名时，成员变量被隐藏，使用 this 调用成员变量</font>

    <font color="red">对成员变量的操作只能放在方法中（即，不能在class中单独为成员变量赋值：a = 10），必须在声明时赋值：int a =10</font>

    
    
    * 构造方法与对象的创建
    
      对象：用类声明的变量，声明后必须创建对象
    
      
    
      构造方法(不会被继承）：
    
      ​	<font color="green">构造方法与类名相同，而且没有方法类型</font>
    
      ​	一个类中可以有多个构造方法他们参数不同（个数，类型）
    
      ​	如果类中没有编写构造方法，系统默认构造方法（无参数，无语句）
    
      创建对象：
    
      ​	声明对象：类名 对象名
    
      ​	分配变量：new 构造方法    计算分配变量的引用，将引用赋给对象

* 使用对象

  对象.变量 ：访问变量    new A().height = 10 //使用匿名对象访问变量（最好不要使用匿名对象）

  对象.方法 ：访问方法

  <font color='green'>将对象赋值给对象其结果是：两对象使用统一引用值，即指向同一组实体（变量组），没有被引用指向的实体即为垃圾实体。</font>
  
  

* 对象组合 

  一个类把某个对象作为自己的成员变量

  ```
  class SIM{}
  class Phone{
  	SIM sim;
  	void setSIM(SIM sim){
  		this.sim = sim;
  	}
  }
  ```



* 实例成员与类/静态成员

  用static 关键字声明的成员变量称为类变量；static int x

  不同的对象实例变量互不影响；但类变量是公共的，会影响到所有对象；

  类变量可以通过类名直接访问；

  <font size=4>静态变量随编译一起写入内存，实例变量在创建对象时才加载进内存</font>



* 实例方法与类/静态方法

  用static关键字声明静态方法；：static void fun(){ }

  对象调用实例方法；类名调用静态方法；

  如：Math.pow(double a,double b)就是静态方法；

####  java.tuil.Arrays类

* public static void sort ( double a[ ] ) :将double型数组a按升序排序（修改原列表）

* public static void sort ( double a[] , int start , int end )  : 

  将数组a中的索引为 start～end-1 的元素升序排序

* public static int binarySearch( double []a , double number)  

  使用二分法判断参数number是否在数组a（必须已经排序）中；

  存在则返回number的索引，不存在则返回一个负数。

* Arrays.toString( double a[] ) 返回String型的数据



---

#### 方法重载

​	重载：一个类中有多个同名方法，但他们参数不同(参数类型，参数个数）；

​	返回类型（int、float、void...)和参数名称（x，y，...)不参与比较（即，返回类型和参数名的不同不算是参数不同）

​	重写与继承有关

​	<font size=3>eg.	Arrays.sort(int a[])、Arrays.sort(double a[])、Arrays.sort(double a[] , int start , int end)</font>

注意重载方法歧义

```
class Dog{
	void cry(int x,double y){
		...
	}
	void cry(double x,int y){
		...
	}
}

cry(10,10.0)和cry(10.0,10)可以被执行，但是cry(10,10)产生歧义，无法编译
```



#### this关键字（代表某个对象）

* this出现在实例方法和构造方法中，不能出现在类方法。

* 在构造方法中，this <==> 使用本构造方法所创建的对象

  ```
  class A{
  	A(){
  		this.B(); <==> A a = new A() ;  a.B();
  	}
  	void B(){ ... }
  }
  ```

  

* 在实例方法中，this <==> 正在调用本实例方法的对象

  ```
  class A{
  	void B(){
  		this.C(); <==> ? x = new ?(); x.C()
  	}
  	void C(){...}
  }
  ```

  this.成员变量

  类名.类变量

  在实例方法中通常省略this和类名；<font color="red">（局部变量名与成员变量名重复时要使用this来调用实例变量）</font>

  

#### 包（package）

​		不同源文件可能出现同名的类，使用包名来区分这些同名类（属于不同的包）

​		声明包：``` package learning.computeArea;```

​		其对应的文件文件夹：``` .../learning/computeArea/```

* 编译时，需要在包（learning）的上一级运行主类

  ```
  cd .../leaning
  javac *.java
  cd ..
  java learning.computeArea.Main
  ```



#### import 

​	引用类库中的类（与当前源文件不在同一包）

* 引入包中的类和接口

* 写在package声明之后，源文件类定义之前。

* 常见包

  ```
  java.lang	：基本语言类(自动引入)（Math,System等）
  javax.swing	：窗口类
  java.io		：输入输出类
  java.util	：实用类
  java.sql	：操作数据库的类
  java.net	：网络功能类
  ```

* 通配符引入所有类：import java.util.*

* 可以不使用import，带包名使用类

  ```java.util.Date date = new java.util.Date();```

* 引入包过多会影响编译速度，但不影响程序性能

* 引入用户自定义的包（learning.computeArea)

  需要在CLASSPATH中添加包的位置

* <font color="red">CLASSPATH</font>

  ```
  rt.jre 是类库中的类
  .;	可以加载当前目录无名类
  ```

* 在同一目录下的无包名类可以相互使用，无包名类可以使用import来使用有包名类，但有包名类不能使用无包名类



#### 访问权限

* 访问权限：是否允许对象通过“.”运算符操作自己的变量或调用类中的方法

* <font color = "red">类中的实例方法总是可以操作该类的实例变量和类变量；类方法总是可以操作该类中的类变量，与访问权限无关。</font>实例方法操作成员变量，类方法操作类变量，不受权限控制

* 访问权限修饰符

  * private

     私有变量和私有方法,不能被继承

    <font color="red">用某个类在另一个类中创建对象后</font>，该对象不能<font color="red">直接</font>访问自己的私有变量和私有方法。不在本类中就不能直接操作本类的私有变量和私有方法

    私有变量不可以直接访问（对象.私有变量）,可以调用方法来操作。

  * protected

    受保护的成员变量和方法

    在<font color="red">同包</font>中的另一个类中声明的本类对象可以访问本类受保护变量和方法

    在继承中，不在同一包中的public 和 protected可以被继承，但是，子类继承父类的protected方法，只有当创建子类对象的文件与该子类的父类在同一包中，对象才能访问子类继承的protected。只要子类与该对象在同一包中，对象就可以访问子类自己的protected。

  * public

    共有变量和共有方法

    在另一个类声明的本类对象中可以访问本类的共有成员变量和共有方法

    ```对象.共有实例变量，对象.共有实例方法```

    ```类名.共有类变量，类名.共有类方法```

  * 友好变量和友好方法（没有任何权限修饰符）

    在<font color="red">同包</font>中的另一个类中声明的本类对象可以访问本类友好成员变量和友好方法

    <font color="red">友好与受保护的区别</font>：在继承中，不在同一包中的子类与父类，只有public和protected可以继承。

  权限级别：public > protected > 友好 > private

* <font color="red">public 类与友好类（不可以用其他修饰符修饰类）</font>

  友好类：必须在同一包中才能在另一个类中创建本类的对象



#### 基本类型的类封装

​	基本数据类型：boolean byte short char int long float double的类封装在java.lang

* Double类、Float类、Integer…

  构造方法：Double（double num），Float（float num）...

  实例方法：doubleValue()  , floatValue() ...返回该对象含有的double，float...型数据

* Character类

  构造方法：Character(char c)

  实例方法：charValue()

#### 对象数组

```
class A{...}
class Main{
	public static void main...{
		A a[];//声明A型数组
		a = new A[10];//为数组分配长度
		for (int i=0;i<a.length;i++){
			a[i] = new A();//生成A的对象
		}
	}
}
```



#### jre扩展与jar文件

​	将类打包为jar文件，放入扩展位置（jre/lib/ext/)即可使用import语句导入自定义类

```
两个类文件TestOne , TestTwo 的包名为 moon.star
编译两文件
编写hello.mf到包名的上一级:
Manifest-Version: 1.0
Class: moon.star.TestOne moon.star.TestTwo
Create-By: 1.8

在包名的上一级目录执行
jar cfm Jerry.jar hello.mf moon/star/TestOne.class 	     moon/star/TestTwo.class
经生成的Jerry.jar复制到/jre/lib/ext/
```



---



### Chapter 5

* 子类与继承

* <font color="red">任何类都是Object类的子类。</font>(所以一般不写extends Object)

  ```class A <==> class A extends Object```

* 子类继承父类的成员变量和方法（构造方法不被继承）
  * 子类与父类在同一包中：仅private不能继承
  * 子类与父类在不同包中：仅public和protected可以继承

* **instanceof运算符**

  计算左边的对象	是 否是	右边类（或子类）  创建的对象

  返回 true 或 false

  ```java
  new Integer() instanceof Object;
  ```

  

* <font color="red">成员变量的隐藏和方法重写</font>

  子类声明的成员变量与从父类继承而来的成员变量  同名(类型可以不同)，则子类隐藏父类的成员变量。<font color="green">子类继承的方法一定是操作 继承或隐藏的成员变量</font>

  隐藏变量和方法可以使用super，私有变量和私有方法,不能被继承和调用

  <font color="red">子类继承的方法只能操作子类继承和隐藏的成员变量；<br/>子类新定义的方法可以操作子类继承和新声明的成员变量，但无法操作隐藏成员变量</font>

* #### <font color="red">方法重写</font>

  方法重载：一个类中出现多个同名方法但他们的参数不同（返回类型和参数名的异同不算）。

  方法重写：子类通过方法重写隐藏 继承（必须有权继承）的父类方法（方法覆盖）

  ​					子类定义的方法与它继承的父类方法头完全相同（类型，名字，参数）

   							或者该方法是父类方法的子类型（父类方法的类型是类，重写方法允

  ​							 许是 该类的子类）

  <font color="red">static 方法参与继承但不能重写</font>

  重写的方法可以操作继承的成员变量，继承的方法，子类新声明的变量和方法，但无法操作被隐藏的变量和方法。
  
  <font color="red">重写方法的访问权限只允许提升，public > protected > 友好>private</font>

* #### super关键字

  在子类中使用super关键字调用被隐藏变量和方法

  * 子类不继承父类的构造方法，子类在其构造方法中使用super类调用父类构造方法，且super必须是第一条语句。（子类的构造方法默认句为 super( ) ; 可以不写）
  * 当父类有多个构造方法，无参数的默认构造方法不再提供，此时子类的super()会出错，<font color="red">父类有多个构造方法时应当手动编写 无参数的构造方法</font>

* #### final关键字

  * 不能和abstract同时使用

  * final类不能被继承(eg.String类就是final类)
  * final方法不能重写（隐藏）
  * final变量是常量，不允许发生变化（ ```for (final int i=0;i<=5;i++)报错 ```）

* #### 对象的上转型

  把子类创建的对象b的引用传给父类声明的，对象a，则该父类对象a称为子类对象b的上转型

  ```
  A a；
  B b = new B(); a=b;
  或者 a = new B();
  ```

  * 上转型对象不能访问子类自己的变量和方法
  * 上转型对象能够调用 继承变量，继承方法，隐藏变量和重写后的实例方法
  * 上转型不可以调用重写的静态方法，只能调用父类的静态方法

  不同子类对象的上转型（父类对象）调用相同的方法（方法被不同子类重写）实现不同的功能。

* #### abstract关键字：修饰类，方法

  * 不能和final同时使用

  * <font color="red">abstract 方法必须是实例方法，只声明,没有方法体,只能在abstract类中</font>

    ```abstract int a(){};```

  * abstract 类 可以有abstract方法和非abstract方法

    * 不能使用new创建对象

    * 子类是非abstract类，则子类要重写abstract类方法，给出具体方法；

      子类也是abstract类，则可以重写，也可以继承abstract方法

    * abstract类的对象可以成为其子类对象的上转型

---

### Chapter 6

​	接口（interface）与实现（implements）

##### 类不能有多个父类，但可以有多个接口

* 接口声明和接口体

  ```interface 接口名{}```

* 接口体

  ​	所有常量（final）都是 public static final    可省略

  ​	所有方法都是public abstract		可省略

  

* 实现接口

  class A implements 接口名1，接口名2

  类实现接口

* 重写接口方法

  非abstract类实现interface 必须重写abstract方法（权限为public）

  abstract 类可以重写方法，也可以直接拥有方法

  父类实现接口，子类自然实现接口

  接口可以被子接口继承（全部常量和方法）

* 接口回调

  把实现某一接口的类创建的对象的引用赋值给该接口声明的接口变量，该变量就可以调用被类实现的方法，但无法调用非接口方法

  ```java
  interface A{...}
  class B implements A{...}
  class C {
  	A a;
  	void setA(B b){
  		this.a = b;//传入参数为 new B()即可实现接口回调
  	}
  }
  
  ```



<font color="red">interface与abstract类的比较</font>

```
interfance 只能有public abstract方法 和 public static final常量
abstruact类有abstruact方法和非abstruact方法，以及常量和变量
```

各类都有：使用abstract类，有的类没有：使用interface

---

### Chapter 7

​	内部类与异常类

* 内部类

  在类（外嵌类）中定义另一个类（内部类）

  * 外嵌类的成员变量和方法在内部类有效
  * 内部类体中不能声明类变量和类方法，外嵌类可以声明内部类对象
  * 内部类仅供他的外嵌类使用，其他类不能使用某个内部类声明对象
  * static 内部类不能操作外嵌类的实例成员变量，其他类可以通过外嵌类访问static内部类来创建对象

* 匿名类

  匿名类是一个子类，没有类名，所以无法声明对象，但可以创建对象（使用父类构造方法），匿名内的父类可以是接口，抽象类或其他父类

  ```
  A是类
  new A(){匿名类类体}就是A的子类创建的对象
  eg. 
  void f(A a){...}
  调用时：
  xxx.f(new A(){ 匿名类体} )；
  用于临时重写方法
  ```

  * 匿名类可以继承和重写父类方法
* 匿名类必然是在某个类中创建对象，所以一定是内部类
  
  * 匿名类使用父类构造方法
* 匿名类类体不能声明static变量和方法
  
* 匿名方法（Lambda）

  把方法作为参数传递

  ```java
  Judge j1 = new Judge(){//匿名类
      public boolean test(int i){
          return i>10;
      }
  }
  去掉new 类名、方法类型、方法名
  只保留方法参数和方法体
  参数和方法体之间使用 ->
  去掉return 和 {}
  去掉参数类型，只有一个参数时，去掉参数（）
  Judge j4 = i->i>10;//匿名函数
  xx函数(i->i>10);将匿名函数作为参数
  
  if(a>b)
      return 1
  else
      return -1
  匿名写法：(a>b)?1:-1
  ```

  Lambda调用自身静态方法：

  ```java
  h->Judge.fun(h); or Judge::fun;
  ```

  调用实例方法

  ```java
  Judge j = new Judge();
  j::fun
  ```

* 异常类(Exception类)

  异常类导致程序转向，部分语句不执行

  异常对象调用下列方法查询异常内容

  ```
  public String getMessage()
  public void printStackTrace()
  public String toString()
  ```

  ```java
  try{
  	可能发生异常的语句;
  }
  catch(Exception的子类 e){//各exception的子类不能再有父子关系，否则只写一个
  	发生异常后的输出语句;
  }
  catch(Exception的子类 e){...}
  finally{//有无异常都会执行（try-catch语句中出现了System.exit(0)时不执行）
  	
  }
  ```

  自定义异常

  ```java
  class Aexception extends Exception{ void getException }//异常类的子类
  class B{
  	void f() throws Aexception{//声明可能出现的自定义异常
  		throw new Aexception();//抛出异常对象
  	}
  }
  
  class Main{
  	B b = new B()
  	try{ b.f() }//可能出现异常的方法
  	catch(Aexception e){ e.getException()}
  	finally{...}
  }
  ```

* 断言

  assert 声明断言句，java解释器默认关闭断言（java -ea mainClass启用断言）

  断言的格式：

  ```
  assert 布尔表达式  //false则结束程序
  assert 布尔表达式:信息表达式 //false则结束程序并输出信息
  ```