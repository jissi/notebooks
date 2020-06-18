<h1>Thymeleaf语法</h1>


* 引入thymeleaf命名空间：```<html lang="en" xlmns:th="http://www.thymeleaf.org"```

* 取值 ```<p th:text="${message}">默认</P>```

* 循环（本身参与循环） ```<li th:each="user:${users}">默认<li>```

  ```html
  <table>
      <!--每次循环都会生成此标签-->
      <tr th:each="user:${user}">
          <td th:text="${user.name}">姓名为空时显示</td>
      </tr>
  </table>
  
  th:onclick="'selectType('+ ${goods.id} + ')'"
  ```

* URL

  * 静态文件使用 ```th:src  | th:href``` 

    ```html
    <script src="jquery.js" th:src="@{/webjar/jquery/3.x/jquery.js}"></script>
    <link href="bootstrap.css" th:href="@{/webjar/bootstrap/3.x/bootstrap.css}"/>
    ```

  * href链接使用 ```th:href```

    ```xml
    <a th:href="@{/user}">/user</a>
    <a th:href="@{/user/(id=${user.id})}">/user?id=2</a>
    <a th:href="@{/user/(id=${user.id},type=${user.type})}">/user?id=2&type=0</a>
    <a th:href="@{/user/}"
    ```

  
  
* fragment使用

  ```html
  头部
  <header th:fragment="commons::head(title)">
      <meta charset="UTF-8">
      <title th:replace="${title}"></title>
  </header>
  中间
  <div th:fragment="commons::content(num)"></div>
  
  使用
  <head th:replace="commons::head(~{::title})">
      <title>Home</title>
  </head>
  <div th:replace="commons::content(1)">
      
  </div>
  ```

  