# Node.js

服务端运行js、非阻塞式IO

[TOC]

---

### 一、示例

```js
vim server.js
var http = require('http');// 导入http模块
var url = require('url');//导入url模块，用来解析请求url
var querystring = require('querystring');//导入querystring模块，用来解析请求参数
function service(request,response){
    var args = url.parse(request.url).query;//获取请求参数
    var params = querystring(args);//将参数反序列化为对象
    // 控制台输出url、请求参数
    console.log('url: '+ request.url);
    console.log('method: '+request.method);
    console.log('id: ' + params.id);
    // 返回响应
    response.writeHead(200,{'Content-Type':'text/plain'});
    response.end("Hello Node.js");
}
var server = http.createServer(service);
server.listen(8080);

node server.js  启动服务并访问8080端口
```



### 二、模块

引入别人写的js

```js
vim goGet.js
function doget(){ console.log('do get');}
exports.get = doget;//到处函数doget为get，否则导入该模块时该函数不可用

vim server.js
.....
var doget = require('./goGet');//导入模块
doget.get();//调用模块函数
```



### 三、路由

```js
vim router.js //路由处理函数（模块）
function route(map,path){//从map中找到path对应的值(响应内容)
    if(type map[path] === 'function'){
        return map[path]();//返回path对应函数的返回值
    }else{
        return path + 'is not defined';
    }
}
exports.route = route;

vim hanlder.js //路径对应的函数
function getall(){ return 'get all';}
function getone(){ return 'get one';}
exports.getall = getall;
exports.getone = getone;

vim server.js //服务器模块
var http = require('http');
function start( route,map){
    function service(req,rep){
        var path = req.url;//获取请求路径
        var html = route(map,path);// 获取请求路径对应的信息
        rep.writeHead(200,{'Content-Type':'text/plain'});
        rep.write(html);//返回响应
        rep.end();
    }
    http.createServer(service).listen(8080);
}
exports.start = start;

vim index.js //启动模块
var hanlders = require('./handler');
var router = require('./router')
var server = require('./server');
// 创建 路由与函数的映射
var map = {};
map['/getall'] = handlers.getall;
map['/getone'] = handler.getone;

server.start(router.route,map);




```

