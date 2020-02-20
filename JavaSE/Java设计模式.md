<h1>Java设计模式</h1>





---

1. ### 工厂模式

   普通工厂：一个方法）

   多个工厂：多个工厂方法）

   > 实例化工厂，
   >
   > 普通工厂 ：向工厂方法传递参数
   >
   > 多个工厂：调用相应的方法

   静态工厂：工厂中的方法是静态的，无需实例化工厂对象

   应用：getInstance()

2. ### 抽象工厂

   工厂的工厂

   

3. ### 适配器模式

   接口不使用时，实现接口，重写接口方法

   > 特点：
   >
   > 适配器对象实现原有接口
   >
   > 适配器对象组合一个实现新接口的对象（这个对象也可以不实现一个接口，只是一个单纯的对象）
   >
   > 对适配器原有接口方法的调用被委托给新接口的实例的特定方法

4. ### 装饰者模式

   动态地把职责附加到已有对象上去，实现功能扩展

   Java IO中，BufferedReader装饰了InputStreamReader

   ```BufferedReader  input = new BufferedReader(new InputStreamReader(System.in))```

   ```InputStreamReader读取字符流；BufferedReader从字符流读取内容并缓存```

5. 

