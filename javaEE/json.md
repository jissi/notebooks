### JSON （Java Script Object Notation）

javaScript对象表示法，是一种存储数据的方式

json对象由键值对构成，键用双引，值是任意js数据类型：  String,Boolean,Number,Array,Object

---



* 创建JSON对象

  ```javascript
  var j = {"name":"json","age":15}
  ```

* 访问JSON对象（.key）

  ```javascript
  j.name
  j.age
  ```

* JSON数组 ```[ { } ,{ },{ } ]```

  ```JavaScript
  var j_ls = 
      [
          {"name":"json","age":15},
          {"name":"js","age":10},
  	]
  ```

* 访问json数组（[index].key）

  ```javascript
  j_ls[0].name
  ```

---

* 对象转换

  js对象：Number,String,Array,Date,Math和自定义对象

  JSON就是自定义对象

  * 字符串转JSON对象（eval参数必须以“(”开始”)“结束）

    ```JavaScript
    字符串拼接可以得到json结构的字符串
    var s1 = "{\"name\":\"json\"";//反斜杠转义
    var s2 = ",\"age\":15}";
    var s3 = s1+s2;
    var j = eval("("+s3+")");//拼接括号是必须的
    ```

  * JSON对象转字符串

    ```JavaScript
    json对象是一个js对象，直接打印看不见内容
    JSON.stringify(j);//json对象转String
    ```



