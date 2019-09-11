### HTTP

<font size=4>超文本传输协议</font>

**TCP三次握手：** 

```
TCP协议提供可靠地连接服务，采用三次握手建立连接
1. client发送syn包到Server，并进入SYN_SEND状态，等待Server确认
2. Server收到syn包，发送SYN+ACK包到client，并进入SYN_RECV状态
3. client收到SYN+ACK包，向Server发送确认包ACK，完成后，Server和client进入establish状态。
完成三次握手，client和Server开始传送数据
```



URL：统一资源定位器```组成：应用层协议://主机IP或域名/资源路径```

URI：同一资源标识符：标识要访问的网络资源

http请求协议：

```
第一行：请求方式 URI 协议版本
请求头部：
	Host:
	User-Agent:
	Accept:
	Accept-Language:
	Accept-Encoding:
	Referer:
	Connection:
	空行分隔
请求数据：
	form data：
```

http响应协议：

```
第一行： 协议版本 状态码 描述
消息报头：
	Content-Length:长度
	Content-Type:内容格式
	Date:日期
	Server:服务器类型
	以下三个响应头可以禁止浏览器缓存网页
	Pramgma:no-cache  
	Cache-Control:no-cache  
	Expires:0  网页过期时间
	空行分隔
响应正文：
	html,css,js或其他内容
```

响应状态码：

```
200 响应成功
301 客户端跳转（永久）
302 客户端跳转（临时）
304 资源未被修改
404 页面不存在
408 请求超时
500 服务端错误
```

http采用MIME协议来规范 请求|响应正文的数据格式

```html
请求头和响应头的  Conten-type：text/html  指定了正文的MIME数据类型为 .html
```

##### Http请求参数

写在URL后 以 ？开始 用 & 分隔参数

提交方式:
http://localhost/index.html**?参数1=值&参数2=值**

* 以get方式提交

  ```
  请求参数：http请求第一行的URI之后
    get /index.html?p1=1&p2=2 http/1.1
  ```

* 以post方式提交

  ```
  请求参数：位于请求正文
   第一行
   请求头
   请求正文：.....   p1=1&p2=2
  ```

  