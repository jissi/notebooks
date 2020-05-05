# WebSocket

基于TCP的网络协议，实现浏览器与服务器全双工通信：运行服务器主动向客户端发送消息

HTTP的缺陷：只能有客户端发起请求，服务端再返回响应



---

* 导入maven依赖

  ```xml
  org.spring.boot
  spring-boot-starter-websocket
  ```

  

* webSocket配置类

  ```java
  @Configuration
  public class WebSocketConfig{
      @Bean
      public ServerEndpointExporter serverEndpointExporter(){
          return new ServerEndpointExporter();
      }
  }
  ```

* webSockerServer  相当于Controller

  ```java
  @Component
  @ServerEndpoint("/ws/{id}") //建立通信的路径
  public class WebSocketServer{
      //用来存放建立的会话
      private static ConcurrentHashMap<String,WebSocketServer> map = new ConcurrentHashMap<>();
      //当前会话
      private Session session;
      //当前会话id
      private String id = "";
      
      @OnOpen //建立通信成功时的操作：向map中存入当前会话
      public void OnOpen(Session session,@PathParam("id") String id){
          this.id = id;
          this.session = session;
          if(map.containsKey(id)){
              map.remove(id);
          }
          map.put(id,this);
      }
      
      @OnClose //关闭通信时的操作
      public void OnClose(){
          if(map.containsKey(this.id)){
              map.remove(this.id);
          }
      }
      
      //@OnMessage 收到客户端消息
      //@OnError 
      
      //向当前客户端发送消息
      public void sendMessage(String message) throws IOException{
          this.session.getBasicRemote().sendText(message);
      }
      
      public static void send(String message,@PathParam("id") String id) throws IOExcpetion{
          map.get(id).sendMessage(message);
      }
  }
  ```

  



* 客户端建立连接

  ```js
  var url = ws://xxxxxxx;
  var socket = new WebSocket(url);//建立连接
  socket.onopen = function(){ ...... };//连接成功操作
  socket.onmessage = function(){....};//收到服务端消息
  //onclose,onerror
  ```

  