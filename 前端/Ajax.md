### AJAX (Asynchronous Javascript And XML)

实现异步刷新

* ajax请求和响应

  ```javascript
  浏览器：
  	创建 XML HttpRequest
      设置响应函数
      设置访问页面
      执行访问
  服务端：
  	调用响应函数
      判断是否响应成功
      获取相应文本
      显示相应文本
  ```

* XHR（xml htto request）

  XHR对象是一个js对象，类似一个隐藏的线程，ajax通过它做到无刷新效果

  ```javascript
  var xmlhttp = new XMLHttpRequest();
  ```

* 设置响应函数

  ```JavaScript
  xmlhttp.onreadystatechange = checkResult ;//指定使用checkResult函数处理来自服务器的响应
  ```

* 发出请求

  ```JavaScript
  xmlhttp.open("GET",url+参数,true);//启动请求准备发送
  //open(请求类型，请求地址?参数1=val&参数2=val，是否异步发送)
  xmlhttp.send(null);//发送请求，由于参数已经通过上面的get方式放在url里，所以当前send参数为空
  
  用post发送请求时，要用到send；
  xmlhttp.open("POST",url,true);
  xmlhttp.setRequestHeader("")
  xmlhttp.send("user="+username+"&password="+password);
  ```

* 处理响应信息

  ```JavaScript
  //前面设置了响应函数为checkResult
  function checkResult(){
      if(xmlhttp.readyState==4&&xmlhttp.status==200){
          document.getElementById("checkResult").innerHTML = xmlhttp.responseText;//设置要刷新的内容
      }
  }
  
  xmlhttp.readyState 4 表示请求完成
  xmlhttp.status 200   表示响应成功
  xmlhttp.responseText 获取服务器返回的响应内容
  ```

  

