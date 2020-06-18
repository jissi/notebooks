# JavaScript

[TOC]



 用于网页与用户的交互，完整的js由js语言本身，BOM，DOM组成

### 一、基本语法

#### 1.1注释与变量

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
  ES6 中新增了块级作用域。
  块作用域由 { } 包括，if语句和 for语句里面的{ }也属于块作用域
  var x=10; //可以跨块访问，不能跨函数访问
  let x = 10; // 只能块内访问
  const x = 10;// 定义常量，不能修改，只能块内访问
  ```

* 调试

  ```javascript
  alert("xxx");运行时弹窗，表示alert以上代码可以运行
  浏览器调试，f12打开Console，f5刷新，查看Console
  console.log();把信息输出到Console，不影响用户其他操作
  ```

#### 1.2 数据类型

```javascript
基本数据类型也是伪对象
Boolean   true|flase
Number    数值型
NaN  not a number
String    字符串（js没有字符概念，单引双引都是字符串）
var       动态类型（根据赋值自动判断类型）
null	  空对象或对象不存在,常用
undefined 声明了但没有赋值,仅仅在判断函数参数是否传递的情况下有用。

Array     数组 x = [1,2,3] <==> x = new Array(1,2,3)
对象       由k-v组成的无序集合,k都是Striing类型  x = { name:'hi',age:10 }


== 会先转换数据类型再比较
===表示绝对等于即，两边的值和类型都相等，1=="1" : true,
    1==="1":false
!== 绝对不等于

？: 三目运算符,有三个操作数，第一个为true就返回第二个，否则返回第三
1<2?1:0  返回1
```

变量类型判断：```typeof 变量名```

##### Number

```JavaScript
var x = new Number(123)//创建数字对象
Number.MIN_VALUE //返回Number支持的最小值
toFixed(x);//返回小数表达
toExponential(x);//返回科学计数表达
isNan(x);//x是否为NaN
valueOf(x);//返回基本类型
typleof x;//返回数据类型Object
```

##### String

```JavaScript
var s = 'str';或 var s = new String("str");//创建String对象
!!! 字符串是不可变的,给指定索引赋值不会报错，但没有效果
s.length;//返回字符串长度
s.charAt(0); <==> s[0]//返回指定位置的字符
s.charCodeAt(0);//返回指定位置的字符对应的Unicode码
s.concat("str");//返回字符串拼接后的字符串
s.indexOf("a")/lastIndexOf("a");//返回子串出现的位置 -1表示没有
s.localeCompare("str");//返回两字符串是否相同，0相同

s.substring(0,3);//返回截取0~3个字符构成的子串substring(3)从3到结束
s.split("分隔符"[,数组长度]);//返回以分隔符分割后的数组
s.replace(search,replacement);//将search替换为replacement（只替换第一个search）
s.repalce("/a/g,"o");//将所有a替换为o
s.toUpperCase()/toLowerCase()
var t = 'abc ${s}';//应用字符串s ES6支持
使用 \ 转义字符 :
          \\ -> \
          \n -> 换行
          \t -> 制表
```

##### Array

```JavaScript
var ls = new Array();//创建空数组
var ls = new Array(5);//创建长度为5的数组
var ls = new Array(1,2,3,4);//指定元素创建数组
ls.length;//返回数组长度 
ls.length = 5 //会改变数组大小
ls[10] = 10 //通过索引操作元素时如果索引超出范围，数组大小变大
 ls[0] 
ls.indexOf(10)// 返回10第一次出现的位置
for(i in ls){};//遍历数组，i是index

ls.sort();// 默认排序，将所有元素转String再排序
ls.sort(comparator);自定义排序方法comarator返回正负数字
ls.reverse();

ls.join("分隔符");//数组元素间设置分隔符，返回String
ls.concat(ls2);//返回连接后的数组
ls.shift('1','2');ls.unshift();//在数组头插入或取出
ls.push('1','2');ls.pop();//在最后的位置插入和取出
ls.slice(1,3);//获取1~2个元素构成子数组  与 str.substring()类似,不传参时可以用来复制数组
ls.splice(3,2);//从3开始，删除两个元素
ls.splice(3,0,1,5);//从3位置开始，删除0个元素，再向此位置插入1和5

// ---  高级方法 
//---map(函数名)将操作映射到所有元素， reduce(接收两个参数的函数)将操作向后执行---
function pow(x){return x*x}
var arr = ls.map(pow);//对数组中没有元素进行pow运算，并返回处理后的array
console.log( ls.map( (x) => x*x ))//箭头函数写法
console.log( ls.reduce( (x,y) => x+y ) )//对数组求和
//---filter 过滤掉某些元素
console.log( ls.filter( (x) => x > 10) )//只要大于10的元素
ls.filter( function(el,index,self)){ //回调函数，实现去重
           return self.indexOf(el) === index;//indexOf返回第一次出现的位置
          })
//---sort 排序
ls.sort((x,y) => y-x)  //实现倒序排序
//--- every()
ls.every((x)=> x>10 )//返回布尔型，数组所有元素都大于10?
//--- find()，findIndex()
ls.find( (x) => x === 10)//返回数组中值为10的元素，没有就返回undedined
ls.findIndex(...)//返回元素索引，否则返回-1
```

##### 基本数据类型转换

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

##### 对象

```javascript
// k-v组成的无序集合
var person = {
    name:'p1',
    age:20,
    gender:'female'
    'lan-level':'CET4'//属性名包含特殊字符时使用''
    getAge:function(){ //方法
        var that = this//注意，this只在本方法内指向当前对象
        function f(){
            // this.age; this在此不指向person对象，返回undedined
            return that.age;
        }
        return f();
    }
}
person.name 或 person['name'] // 访问属性,属性不存在时返回undefined
person['lan-level'] //访问特殊字符属性
//---操作属性
'name' in person //检查属性(包含继承Object而来的属性)是否存在，返回true\false
person.hasOwnProperty('name') //检查自身是否拥有属性
delete person['lan-level'] //删除属性
delete person.name
person.school = 'xx' //新增属性
// 调用方法
persion.getAge();
```

##### Map和Set（ES6支持）

```javascript
// 对象的k只能是String类型，所以推出了专门的数据类型
// ---- map
var map = new Map( ['name':'p1'] , ['age':12] )
var map = new Map();
map.set( ['gender','female'] );//新增k-v或修改v
map.has('name')
map.get('name')
map.delete('name')
// ---- set
var set = new Set( [1,2,3] )
var set = new Set();
set.add(4)
set.delete(4)
```

##### iterable (ES6支持)

```javascript
// Array可以通过index实现遍历
for (var index in array){ consolg.log( arry[i] )}
// 为了遍历 Map、Set es6 引入iterable类型
// Array Map Set 都属于Iterable类型 使用 for of 来遍历
for (var a of array){ console.log(a); }
for(var s of set){ console.log(s);}
for(var e of map){ console.log( e[0] +':'+ e[1] )}
// 使用forEach遍历 es5.1支持
// 三个参数：当前元素，索引，指向自己的引用，可以只要element参数，或k,v参数
array.forEach( function(element,index,arr) { console.log(element) })
set.forEach(function(element,sameElement,set){ console.log(element)})
map.forEach(function(k,v,ma){....})
```



#### 1.3函数

```javascript
// -------------------方法传参时限制参数数量，可多可少----
function 函数名(参数){//不需要写参数类型
    方法体；
    for(var i =0 ;i< arguments.length;i++{
        console.log('参数'+i+' : ' + arguments[i] )
        };//方法内置的arguments接收所有传入的参数，类似Array但不是

    //return xx;返回值，没有return时返回undefined
}
var abs = function(参数){...}; //匿名函数
                       
// ------定义变长参数，...rest只能写在最后-------------
function(a,b,...rest){
    console.log(rest);//输出a,b之外的所有参数
}

// 函数内变量与外部变量重名时，使用内部变量

// --------------------apply和call------------------               
fun.apply( person,[]);// fun方法内的this指向person对象，方法参数列表为[]，调用该函数
Math.max.apply(null,[1,2,3]); //调用求最大值函数，this为null(该函数不需要this)
Math.max.call(null,1,2,3); //使用call不用打包参数，直接传入
                       
// --------------------将函数作为返回值---------------
function getFun(arr){
    var sum = function(){
        return arr.reduce((x,y) => x+y );
    }
    return sum;//将函数作为返回值
}
var sum = getFun([1,2,3]);//拿到返回的sum函数，每次调用返回函数对象=== 为false
sum();//调用该函数时才真正计算
                       
// --------------------箭头函数 this指向外部的obj---------------
function (x){ return x+1} <==>  x => x+1
// 多条语句
x => { ....;return xxx}
// 多个参数
(x,y) => x+y;
// 返回对象 对象有{}包围，与箭头函数冲突
x => ( { name:'p'} );
//---------------------闭包 一个函数用到了它作用域外面的变量-------
                       
//---------------------生成器，可以返回多次值的函数---------------
function* range(start,end){// 注意：*号
    for(var s = start ; s<end;s++){
        yield s;//返回s
    }
}
for(var s of range(0,5)){ console.log(s) }
```

#### 1.4 流程控制

```JavaScript
if(){}
else if(){}
else{}
switch:同java switch，每个case 最后都要加break
    
for(var i=0;i<5;i++){}
while(true){}
do{}while(xxx);至少执行一次
forEach(i:list);增强for循环
continue;进入下一次循环
break;退出循环
```



#### 1.5 异常处理

```JavaScript
try{
    
}catch(err){
    err.message;
}//跟java类似，不用写异常类型
```

---

### 二、对象

##### 2.1 Date对象

```JavaScript
var d = new Date();//当前时间
d.getFullYear(),getMonth,getDate,getHours,getMinutes,getSeconds,getMilliseconds
//month，day从0开始
d.getDay();//一周的第几天
d.getTime();//经历的毫秒
d.setFullYear,.....
```

##### 2.2 Math对象

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

##### 2.3 自定义对象

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



##### 2.5 正则

```javascript
\d 匹配一个数字  00\d -> 007
\w 匹配一个字母或数字 j\w -> js
\s 匹配至少一个空格 ' ' 制表符
. 匹配一个字符 js. -> jsp js!
? 匹配0-1个字符
+ 匹配1-n个字符
* 匹配任意个字符 j* -> javascript j
{n} 出现n次
{n,m} 出现n-m次
特殊字符使用\转义 - -> \-
----------------------
eg: \d{3}\s+\d{3,8} 三个数字+至少一个空格+3-8个数字：010 12345
    \d{3}\-\d{3,8} 010-12345
----------------------
[0-9a-z-A-Z\_] 匹配一个数字/字母/下划线
[0-9a-z-A-Z\_]+ 匹配至少一个数字/字母/下划
a|b 匹配a或b
^a  以a开头
a$  以a结尾
----------------------
var re = /^(\d{3})-(\d{3})$/;将匹配到的字符分组
re.exec("123-456");// 分组取出 ['123-456','123','456']
---------------------------------使用正则-----------------
var re = /^\d{3}\_\w\d$/; 方法1 用 / / 包裹正则
var re = new RegExp("^\\d{3}\\_\\w\\d$"); 方法2：注意转义符 要将 \d 改为\\d
re.test("123_a5"); 返回true
// ---- 分割字符
var str = "a b      c";
str.split(/\s+/);// ['a','b','c']
```

##### 2.6 JSON

```javascript
// k使用String类型，v支持 number,String,Boolean,Object,Array,null
var jsObject = {
    name:'p1',
    age:20,
    gender:'female',
    'lan-level':'CET4',
}
// -----------序列化 object -> json
var s = JSON.stringify(jsObject);
{"name":"p1","age":20,"gender":"female","lan-level":"CET4"}
// ----------返序列化 json -> object
var obj = JSON.parse(s);
```



---

### 三、BOM

 浏览对象：Window(窗口)，Navigator(浏览器)，Screen(客户端屏幕)，History(访问历史)，Location(浏览器地址)

##### 3.1 window 表示浏览器窗口

一旦页面加载，自动创建Window对象

```JavaScript
window.innerWidth;innerHeight//返回文档显示区的宽度和高度
window.outerWidth;outerHeight//浏览器宽高
window.open("url");//打开新一个网址
```

##### 3.2 navigator 表示浏览器信息

```javascript
navigator.appName;//浏览器名
		  appVersion;//浏览器版本
          appCodeName;//浏览器内部编码
          platform;//操作系统
          CookieEnabled;//是否启用cookies
          userAgent;//浏览器用户代理头     
```

##### 3.3 screen 屏幕信息

```javascript
screen.width;height;//用户屏幕分辨率
screen.availWidth;availHeight;//可用区域宽高
```

##### 3.4 location url信息

```javascript
location.reload();//刷新页面
location.assign("url");//跳转到指定页面
lication.href;// 当前url
属性：
location.protocol;//协议
		 hostname;
		 port;
		 host;//主机名和端口
		 pathname;//访问的路径
		 hash;//锚点
		 search;//参数列表
```

##### 3.5 document 当前页面

```javascript
document.cookie;//读取当前页面cookie;服务器端在设置Cookie时，应该始终坚持使用httpOnly禁止js读取cookie
document.title("123");// 修改标题 <title>123</title>
document.getElementById("id");//根据标签的id获取一个DOM节点
document.getElementByTagName("p");//获取所有p标签
更多用法间DOM部分
```

##### 3.6 弹框

```JavaScript
alert("警告");//警告框
confirm("再次确认");//确认框，返回true，false
var name = prompt();//输入框,返回输入的内容
```

##### 3.7 计时器

```javascript
setTimeout(函数名,倒计时毫秒);//只执行一次定时任务
var t = setInterval(函数名，重复间隔毫秒);//每隔一段时间执行一次
clearInterval(t);//终止定时任务
在部分浏览器中，setInterval中执行的document.write()只能看到一次执行效果
```

---

### 四、HTML DOM

DOM是把html里的各种数据当做对象进行操作的思路

* 节点

  DOM把所有html都转换为节点

  整个文档，元素，元素属性，元素内容，注释都是节点

##### 获取dom节点（对象）

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

节点属性

```JavaScript
节点.nodeName;//返回节点名
节点.nodeValue;//返回节点值，document和tag的值为 null
节点.nodeType;//返回节点类型，document为9，tag为1,属性为2，内容为3，注释为8

var div = document.getElementById("div");
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

##### 更新DOM

* 更新内容

  ```javascript
  var div = document.getElementById("div1");
  // 修改文本
  div.innerHTML = '<h2>123</h2>';//innerHTM 可以修改文本内容和子dom
  div.innerText = '123';只能修改文本，html标签会被转义为字符串，读取文本时不返回隐藏文本
  div.textContent = '123';同上，但是读取文本时返回所有内容
  ```

  

* 更新样式

  ```javascript
  var div = document.getElementById("div1");
  div.style.fontSize = '20px';// js不支持font-size写法，应使用驼峰命名
  div.style.display = "none"
  div.style.backgroundColor = "red";//与css写法不同
  ```

##### 插入DOM

* dom间关系

  ```javascript
  div.parentNode;//返回父节点
  previousSibling;//前一个同胞节点
  nextSibling;//后一个同胞节点
  childNodes;//返回子节点列表，第一个子节点是文本节点
  children;//返回子节点列表，不包含文本节点
  ```

  

* 创建dom

  ```javascript
  var hr = div.createElement("标签名");//创建元素节点
  div.appendChild(hr);//为节点插入子节点
  div.createTextNode("文本内容");//创建文本节点
  div.createAttribute("属性名");//创建属性节点
  div.setAttributeNode(属性节点);//为节点添加属性节点
  ```

* 插入dom

  ```javascript
  div.innerHTML("<p>123</p>")
  div.appendChild(子节点);//追加子节点
  div.insertBefore(新节点，哪个节点前)
  ```

* 替换dom

  ```javascript
  //通过父节点替子节点
  父节点.replaceChild(被替换的节点，替补节点)
  ```

  

##### 删除dom

```javascript
div.removeChild(要删除的节点);
div.removeAttribute;//删除属性节点
//删除文本节点
div.removeChild(childNode[0]);//删除文本节点
div.innerHTML = "";//删除文本节点
```



##### DOM事件

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









