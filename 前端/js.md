### JavaScript

##### 用于网页与用户的交互，完整的js由js语言本身，BOM，DOM组成

* javascript代码必须放在script标签

  ```javascript
  script标签可以放在html的任何地方，一般在head标签里
  javascript一旦加载就会执行
  执行顺序从上到下
  ```

  使用外部文件，文件内不需要写script标签,html通过以下方式引用

  ```html
  <script type="text/javascript" src="path"></script>
  ```

* 注释

  ```javascript
  //单行注释
  
  /*多行
    注释
  */
  ```

* 变量

  ```javascript
  var x=10;
  或 x=10;
  ```

* 调试

  ```javascript
  alert("xxx");运行时弹窗，表示alert以上代码可以运行
  浏览器调试，f12打开Console，f5刷新，查看Console
  console.log();把信息输出到Console，不影响用户其他操作
  ```

* 数据类型

  ```javascript
  基本数据类型也是伪对象
  undefined 声明了但没有赋值
  Boolean   true|flase
  Number    数值型
  String    字符串（js没有字符概念，单引双引都是字符串）
  var       动态类型（根据赋值自动判断类型）
  null	  空对象或对象不存在
  
  ===表示绝对等于即，两边的值和类型都相等，1=="1" : true,
      1==="1":false
  !== 绝对不等于
  
  ？: 三目运算符,有三个操作数，第一个为true就返回第二个，否则返回第三
  1<2?1:0  返回1
  ```
  
  变量类型判断：```typeof 变量名```

* 类型转换

  ```javascript
  var a="123";//字符串对象通过其伪对象的length方法获取长度
  document.write(a.length);
  
  转字符串
  toString();//其他类型转字符串,null.toString()报错
  //Number,Boolean,String都有toString()方法，转为字符串
  String();//String(null)返回null
  
  
  数字转字符串
  var a=10;
  a.toString();//默认转十进制
  a.toString(16);//转16进制
  
  转为数字
  parseInt("str"),parseFloat("str");//转数字
  //以数字开头就返回数字，非数字开头返回NaN
  Number("123");//转数字，包含非数字返回NaN(Not a Number)
  //字符串转数字 "10abc8" -> 10
  
  转为Boolean
  Boolean("123");
  //非空字符串-> true
  //非0数字  -> true
  //非null对象 -> true
  ```

* 函数

  ```javascript
  function 函数名(参数){//不需要写参数类型
      方法体；
      //return xx;返回值
  }
  ```

* 变量作用域

  ```JavaScript
  函数的参数或函数内的变量只在函数内有效
  定义在函数之前的全局变量全局有效
  ```

* 事件

  ```html
  <button onclick="fun()"></button>//点击调用函数
  ```

* 条件语句

  ```JavaScript
  if(){}
  else if(){}
  else{}
  
  switch:同java switch，每个case 最后都要加break
  ```

* 循环

  ```JavaScript
  for(var i=0;i<5;i++){}
  while(true){}
  do{}while(xxx);至少执行一次
  forEach(i:list);增强for循环
  continue;进入下一次循环
  break;退出循环
  ```

* 异常处理

  ```JavaScript
  try{
      
  }catch(err){
      err.message;
  }//跟java类似，不用写异常类型
  ```

---

#### 对象

##### 有着属性和方法的特殊数据类型

* Number对象

  ```JavaScript
  var x = new Number(123)//创建数字对象
  Number.MIN_VALUE //返回Number支持的最小值
  toFixed(x);//返回小数表达
  toExponential(x);//返回科学计数表达
  isNan(x);//x是否为NaN
  valueOf(x);//返回基本类型
  typleof x;//返回数据类型Object
  ```

* String对象

  ```JavaScript
  var s = new String("str");//创建String对象
  s.length;//返回字符串长度
  s.charAt(0);//返回指定位置的字符
  s.charCodeAt(0);//返回指定位置的字符对应的Unicode码
  s.concat("str");//返回字符串拼接后的字符串
  s.indexOf("a")/lastIndexOf("a");//返回子串出现的位置
  s.localeCompare("str");//返回两字符串是否相同，0相同
  s.substring(0,3);//返回截取0~3个字符构成的子串
  s.split("分隔符"[,数组长度]);//返回以分隔符分割后的数组
  s.replace(search,replacement);//将search替换为replacement（只替换第一个search）
  s.repalce("/a/g,"o");//将所有a替换为o
  ```

* Array对象

  ```JavaScript
  var ls = new Array();//创建空数组
  var ls = new Array(5);//创建长度为5的数组
  var ls = new Array(1,2,3,4);//指定元素创建数组
  ls.length;//返回数组长度
  for(i in ls){};//遍历数组，i是index
  ls.concat(ls2);//返回连接后的数组
  ls.join("分隔符");//数组元素间设置分隔符
  ls.push();ls.pop();//在最后的位置插入和取出
  ls.shift();ls.unshift();//在数组头插入或取出
  ls.sort();
  ls.sort(comparator);自定义排序方法comarator
  ls.reverse();
  ls.slice(1,3);获取1~2个元素构成子数组
  ls.splice(3,2);//从3开始，删除两个元素
  ls.splice(3,0,1,5);//从3位置开始，删除0个元素，再插入1和5
  
  ```

* Date对象

  ```JavaScript
  var d = new Date();//当前时间
  d.getFullYear(),getMonth,getDate,getHours,getMinutes,getSeconds,getMilliseconds
  //month，day从0开始
  d.getDay();//一周的第几天
  d.getTime();//经历的毫秒
  d.setFullYear,.....
  ```

* Math对象

  ```javascript
  工具类对象
  Math.E;//自然对数
  Math.PI;
  Math.abs(-1);
  Math.min(1,2);Math.max(1,2)
  Math.pow(3,3);//幂运算
  Math.round(3.4);//四舍五入
  Math.random();//0-1的随机数
  ```

* 自定义对象

  ```JavaScript
  var obj = new Object();//自定义对象
  obj.name="xxx";//定义对象属性
  obj.fun = function(){}//定义对象函数
  obj.fun();//调用函数
  //通过new Object()创建对象每次都要重新定义属性和函数
  
  //通过function设计一个对象
  function obj(name){
      this.name=name;
      this.fun = function(){}
  }
  
  //为已经存在的对象添加新方法
  obj.prototype.new_fun = function(){}
  ```

  

---

#### BOM (Brower Object Model)

##### 浏览其对象：Window(窗口)，Navigator(浏览器)，Screen(客户端屏幕)，History(访问历史)，Location(浏览器地址)

* Window

  一旦页面加载，自动创建Window对象

  ```JavaScript
  window.innerWidth;innerHeight//返回文档显示区的宽度和高度
  window.outerWidth;outerHeight//浏览器宽高
  window.open("url");//打开新一个网址
  ```

* Navigator

  ```javascript
  navigator.appName;//浏览器名
  		  appVersion;//浏览器版本
            appCodeName;//浏览器内部编码
            platform;//操作系统
            CookieEnabled;//是否启用cookies
            userAgent;//浏览器用户代理头     
  ```

* Screen

  ```javascript
  screen.width;height;//用户屏幕分辨率
  screen.availWidth;availHeight;//可用区域宽高
  ```

* History

  ```javascript
  history.back();//返回上次访问的网址
  history.go(-2);//返回上上次访问的网址
  ```

* Location

  ```javascript
  location.reload();//刷新页面
  location.assign("url");//跳转到指定页面
  属性：
  location.protocol;//协议
  		 hostname;
  		 port;
  		 host;//主机名和端口
  		 pathname;//访问的路径
  		 hash;//锚点
  		 search;//参数列表
  ```

* 弹框

  ```JavaScript
  alert("警告");//警告框
  confirm("再次确认");//确认框，返回true，false
  var name = prompt();//输入框,返回输入的内容
  ```

* 计时器

  ```javascript
  setTimeout(函数名,倒计时毫秒);//只执行一次定时任务
  var t = setInterval(函数名，重复间隔毫秒);//每隔一段时间执行一次
  clearInterval(t);//终止定时任务
  
  在部分浏览器中，setInterval中执行的document.write()只能看到一次执行效果
  ```

---

### HTML DOM (Document Object Model)

DOM是把html里的各种数据当做对象进行操作的思路

* 节点

  DOM把所有html都转换为节点

  整个文档，元素，元素属性，元素内容，注释都是节点

* 获取节点（对象）

  ```JavaScript
  var div = document.getElementByID("id");
  document.getElementsByName("name");//返回列表
  document.getElementsByTagName("tag");//返回标签构成的数组
  document.getElementsByClassName("classname");//返回列表
  document.nodeName;//值为 #document
  获取不到返回 null;//可能的原因：节点还没加载
  
  //通过元素节点的 attributes 获取其属性节点列表
  var attr = div.attributes;
  	attr[index].nodeName;//返回节点名
  	attr[index].nodeValue;//返回节点值
  	arrt["color"].nodeValue;//返回指定属性名的值
  
  //通过元素节点的 childNodes 获取其所有子节点，第一个子节点就是内容节点
   var content = div.childNodes[0];//获取内容节点列表
  	content.nodeName;//返回内容节点名
  	content.nodeValue;//返回内容节点值
  or  var content = div.innerHTML;//返回内容节点值
  ```

* 获取元素属性节点

  ```JavaScript
  节点.nodeName;//返回节点名
  节点.nodeValue;//返回节点值，document和tag的值为 null
  节点.nodeType;//返回节点类型，document为9，tag为1,属性为2，内容为3，注释为8
  
  元素节点 var div = document.getElementById("div");
  div.nodeName;//节点名
  var attr = div.attributes;//返回属性列表
  attr[0].nodeName;//第一个属性的名称
  attr[0].nodeValue;//第一个属性的值
  
  //修改元素内容
  var content = div.childNodes[0].nodeValue;//返回内容值
  content = "修改后的值";//通过chileNode[0].nodeValue修改内容值
  var content = div.innerHTML;//获取内容值
  content = "xxx";//通过innerHTML修改内容值
  
  //tag上的自定义属性的值需要通过以下方法访问
  <p id="p" test="这是自定义属性"></p>
  var p = document.getElementById("p");
  p.getAttribute("test");
  p.attributes["test"].nodeValue;
  ```

* DOM样式

  一个元素的style属性即对应的css

  ```javascript
  var d = document.getElementById("div1");
  1. 给元素的style.display赋值
  	d.style.display = "none"
  2. 改变背景色
  	d.style.backgroundColor = "red";//与css写法不同
  ```

* DOM事件

  ```javascript
  this ;//表示当前组件，在调用函数时作为参数
  //阻止事件发生
   1.调用函数时增加return ： return fun()
   2.函数中，判断，return false
   3.触发事件的时候发现返回值为false，就取消事件
  
  
  onfocus;//焦点事件
  onblur;//失去焦点事件
  onclick="fun()";//单击事件
  ondbclick;//双击事件
  onsubmit;//提交事件，监听form元素，写在form属性
  onload;//加载事件，写在img,body属性
  onchange;//变化事件，
  
  onmousedown;//鼠标按下
  onmouseup;//松开
  onmouseover;//进入
  onmousemove;//经过
  onmouseout;//退出
  
  onkeydown;//按下按键，返回键码，可以获取所有键(除了打印键)，不能判断大小写
  onkeypress;//按下按键，返回键码，只能获取字符键，可以判断大小写
  onkeyup;//
  ```

* 节点关系

  ```JavaScript
  parentNode;//返回父节点
  previousSibling;//前一个同胞节点
  nextSibling;//后一个同胞节点
  childNodes;//返回子节点列表，第一个子节点是文本节点
  children;//返回子节点列表，不包含文本节点
  ```

* 创建节点

  ```JavaScript
  var hr = 节点.createElement("节点名");//创建元素节点
  节点.appendChild(节点);//为节点插入子节点
  节点.createTextNode("文本内容");//创建文本节点
  节点.createAttribute("属性名");//创建属性节点
  节点.setAttributeNode(属性节点);//为节点添加属性节点
  ```

* 删除节点

  ```javascript
  父节点.removeChild(要删除的节点);//delete tag node
  父节点.removeAttribute;//删除属性节点
  
  //删除文本节点
  父节点.removeChild(childNode[0]);//删除文本节点
  节点.innerHTML = "";//删除文本节点
  ```

* 替换节点

  ```JavaScript
  //通过父节点替子节点
  父节点.replaceChild(被替换的节点，替补节点)
  ```

* 插入节点

  ```JavaScript
  父节点.appendChild(子节点);//追加子节点
  父节点.insertBefore(新节点，哪个节点前)
  ```



