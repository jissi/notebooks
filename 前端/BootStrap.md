BootStrap

来自Twitter的前端框架，基于HTML，CSS，JavaScript

* 使用Bootstrap

  1. 声明```<!DOCTYPE html>```

  2. 导入jquery (bootstrap需要JQuery才能正常工作)

  3. 导入bootstrap.min.css (定义了各种样式)

  4. 导入bootstrap.min.js (产生交互效果)

     <font color="red">注意导入的顺序：jquery.min.js -> bootstrap.min.css -> bootstrap.min.js</font>

  ```html
  <!DOCTYPE html><!--bootstrap用到了html5特性-->
  <script src="jquery.min.js"></script>
  <link href="bootstrap.min.css" rel="stylesheet">
  <script src="bootstrap.min.js"></script>
  ```

* 套用class实现bootstrap定义好的css效果

  ```html
  <botton class="btn btn-success">按钮</botton>
  ```



---



* 按钮

  按钮样式可以用于

  ```
  <button>
  <a> 
  <input type="button"> 
  <input type="submit"> 
  <input type="reset">
  ```

  样式（class）

  | class                   | 说明     | 样式（圆角矩形） |
  | ----------------------- | -------- | ---------------- |
  | btn                     | 原始样式 | 灰色             |
  | btn btn-default         | 默认按钮 | 白色             |
  | btn btn-primary         | 提交按钮 | 蓝色             |
  | btn btn-success         | 成功按钮 | 绿色             |
  | btn btn-info            | 信息按钮 | 浅蓝色           |
  | btn btn-warning         | 警告按钮 | 橘黄色           |
  | btn btn-danger          | 危险按钮 | 红色             |
  | btn btn-link            | 链接     | 蓝色超链接字体   |
  | btn -btn-lg             | 大按钮   | 灰色             |
  | btn btn-sm              | 小按钮   | 灰色             |
  | btn btn-xs              | 极小按钮 | 灰色             |
  | btn btn-block           | 宽屏按钮 | 加宽灰色         |
  | btn active              | 激活状态 | 灰色，黑色内边距 |
  | btn disabled            | 无效按钮 | 浅灰色，不能点击 |
  | btn disabled btn-danger | 组合样式 | 危险 无效按钮    |

  

* 表格样式

  可用于：table tr td标签

  ```html
  <table class="table">
      <tr class="warning">
          <td class="danger"></td>
      </tr>
  </table>
  ```

  | class                               | 说明                   |
  | ----------------------------------- | ---------------------- |
  | table                               | 基本表                 |
  | table table-striped                 | 带斑马线的表           |
  | table table-bordered                | 带边框的表             |
  | table table-hover                   | 带鼠标悬停的表         |
  | table table-condensed               | 紧凑表                 |
  | table table-striped table-condensed | 多样式混合             |
  | active                              | 激活样式，底色为灰色   |
  | success                             | 成功样式，底色为浅绿色 |
  | info                                | 信息样式，底色为浅蓝色 |
  | warning                             | 警告样式，底色为浅黄色 |
  | danger                              | 危险样式，底色为浅红色 |



* 图片样式

  | class         | 说明   |
  | ------------- | ------ |
  | img-rounded   | 圆角图 |
  | img-circle    | 圆形图 |
  | img-thumbnail | 略缩图 |



* 表单

  class=“form-control” ：去除阴影，边框淡蓝色，输入状态更柔和



* 文本

  | class        | 说明             |
  | ------------ | ---------------- |
  | text-muted   | 浅灰色文字       |
  | text-primary | 强调，浅蓝色文字 |
  | text-sucess  | 成功，浅绿色文本 |
  | text-info    | 提示信息，淡蓝色 |
  | text-warning | 警告，深黄色     |
  | text-danger  | 危险，红色       |

* 背景色

  | class      | 说明       |
  | ---------- | ---------- |
  | bg-primary | 蓝色背景色 |
  | bg-success | 浅绿色     |
  | bg-info    | 浅蓝色     |
  | bg-warning | 浅黄色     |
  | bg-danger  | 浅红色     |



* 其他

  | class      | 说明                     |
  | ---------- | ------------------------ |
  | close      | 关闭按钮（灰色叉）       |
  | caret      | 下拉菜单按钮（黑倒三角） |
  | pull-left  | 左浮动：floa：left       |
  | pull-right | 右浮动                   |
  | show       | 显示                     |
  | hidden     | 隐藏                     |
  | invisible  | 隐藏，占用位置           |

* 栅格布局

  bootstrap栅格系统可以定义行数列数，比table灵活，默认12列

  达到12列所占的列宽时，自动换行

  class=“container”的div替代table，class=“row”的div替代tr

  class=“col-xs-1”代表占1列，col-xs-12代表占12列，一行不必填满

  ```html
  <div class="container">
      <div class="row">
          <div class="col-xs-4"></div><!--占4列，空8列-->
      </div>
  </div>
  ```



---

* 特殊字体符号

  bootstrap提供263种字体图标，<font color="red">bootstrap.min.css 的上一级目录得有fonts目录</font>

* 下拉菜单

  ```html
  <div class="dropdown">
      
      <button class="btn dropdown-toggle" type="button" id="列表的aria-labelledby属性值" data-toggle="dropdown"><span class="caret"></span>
      </button>
      
      <ul class="dropdown-menu" aria-labelledby="button的id" role="menu">
          <li>123</li>   
      </ul>
      
  </div>
  
  <!--role属性仅仅是为了提高可读性，可以不写-->
  ```

  设置不可点击的选项(字体较小)

  ```html
  <li class="dropdown-header" role="presentation">title</li>
  ```

  设置分割线

  ```html
  <li class="divider"></li>
  ```

  禁用选项

  ```html
  <li class="disabled"><a>123</a></li>
  ```



* 按钮组

  所有按钮写在 ```<div class=“btn-group”>```内

  ```html
  <div class="btn-group">
      <button class="btn btn-default"></button>
      <button class="btn btn-default"></button>
  </div>
  ```

  按钮工具栏（class=“btn-toolbar”的div内嵌按钮组）

  ```html
  <div class="btn-toolbar">
      <div class="btn-group"></div>
      <div class="btn-group"></div>
  </div>
  ```

  按钮组大小

  ```html
  <div class="btn-group btn-group-lg"></div>
  <div class="btn-group btn-group-sm"></div>
  <div class="btn-group btn-group-xs"></div>
  ```

  按钮组垂直排列

  ```html
  <div class="btn-group-vertical"></div>
  ```

  复选框

  ```html
  <div class="btn-group" data-toggle="buttons">
      <label class="btn btn-default active">
          <input type="checkbox" checked>默认选中
      </label>
      <label class="btn btn-default">
          <input type="checkbox">未选中
      </label>
  </div>
  ```

  单选框

  ```html
  <div class="btn-group" data-toggle="buttons">
      <label class="btn btn-default active">
          <input type="radio" checked>默认选中
      </label>
  </div>
  ```



* 按钮式下拉菜单

  ```html
  <div class="btn-group">
      <button type="button" class="btn btn-default dropdown-toggle" data-toggle="dropdown"></button>
      <ul class="dropdown-menu"></ul>
  </div>
  ```

  分裂式按钮下拉菜单

  ```html
  <div class="btn-group">
      <button type="button" class="btn btn-default">
          没有下拉功能的按钮
      </button>
      <button type="button" class="btn btn-default dropdown-toggle" data-toggle="dropdown">
          点击下拉<span class="caret"></span>
      </button>
      
      <ul class="dropdown-menu"></ul>
  </div>
  ```

  向上弹出菜单

  ```html
  <div class="btn-group dropup">
      <button type="button" class="btn btn-default dropdown-toggle" id="列表的aria-labelledby">
          向上弹出菜单</button>
      
      <ul class="dropdown-menu" aria-labelledby="button的id"></ul>
  </div>
  ```



* 输入框组

  ```html
  <div class="input-group">
      <span class="input-group-addon" id="header">#</span><!--输入框前的span，写在Input之后就是输入框后span-->
      <input type="text" class="form-control" placeholder="背景文字" aria-labelledby="header">
  </div>
  
  <!--前后都有span-->
  <div class="input-group">
      <span class="input-group-addon">$</span>
      <input type="text" class="form-control" aria-label="Amount">
      <span class="input-group-addon">00</span>
  </div>
  ```

  输入框组尺寸

  ```html
  <div class="input-group input-group-lg"></div>
  <div class="input-group input-group"></div>
  <div class="input-group input-group-sm"></div>
  ```

  整合按钮

  ```html
  <div class="input-group">
      <button type="button" class="..."></button>
      <input>
  </div>
  ```

  整合下拉框

  ```html
  <div class="input-group">
      <div class="input-group-btn">
          <button></button>
          <ul></ul>
      </div>
      <input>
  </div>
  ```




* 导航栏（必须内嵌a标签）

  标签式导航栏

  ```html
  <ul class="nav nav-tabs">
      <li class="active"><a>必须有a标签</a></li>
      <li><a>必须有a标签</a></li>
  </ul>
  ```

  胶囊式标签导航栏

  ```html
  <ul class="nav nav-pills">
      <li class="active"><a></a></li>
      <li><a></a></li>
  </ul>
  ```

  垂直胶囊式导航栏

  ```html
  <ul class="nav nav-pills nav-stacked">
      <li class="active"><a></a></li>
      <li><a></a></li>
  </ul>
  ```

  禁用标签

  ```html
  <li class="disabled"><a></a></li>
  ```

  路径式导航（面包屑导航）

  ```html
  <ol class="breadcrumb">
      <li><a></a></li>
      <li class="active"></li>
  </ol>
  ```

  可切换式标签导航栏

  ```html
  <ul id="myTab" class="nav nav-tabs">
      <li class="active">
          <a href="#要显示的内容的id" data-toggle="tab">tab1</a>
      </li>
      <li><a href="#id">tab2</a></li>
  </ul>
  <div class="tab-content" id="mytabcontent">
      <div class="tab-pane fade in active" id="要显示的内容的id">页面内容</div>
      <div class="tab-pane fade" id="id">页面内容</div>
  </div>
  ```

* 下一页

  ```html
  <nav>
      <ul class="pagination">
          <li>   
              <a aria-label="Previous"><!--上一页-->
              <span aria-hidden="true">&laquo;</span>
          	</a>
          </li>
          <li><a>1</a></li><!--第一页-->
          ...
          <li>
              <a aria-label="Next"><!--下一页-->
                  <span aria-hidden="true">$raquo;</span>
              </a>
          </li>
      </ul>
  </nav>
  
  
  禁用：
  <li class="disabled">
      <a aria-label="Next">
          <span aria-hidden="true">&raquo;</span>
      </a>
  </li>
  
  大小：
  <nav>
      <ul class="pagination pagination-lg"></ul>
  </nav>
  ```

  翻页

  ```html
  <nav>
      <ul class="pager">
          <li><a>上一页</a></li>
          <li><a>下一页</a></li>
      </ul>
  </nav>
  ```

  翻页按钮位于两端

  ```html
  <nav>
      <ul class="pager">
          <li class="previous">
              <a><span aria-hidden="true">&larr;上一页</span></a>
          </li>
          <li class="next">
              <a><span aria-hidden="true">&rarr;下一页</span></a>
          </li>
      </ul>
  </nav>
  ```



* 标签（class=“label label-default”）

  ```html
  classes:
  	label label-default
  	label label-primary
  	label label-success
  	label label-info
  	label label-warning
  	label label-danger
  
  <span class="label label-info">信息标签</span>
  ```



* 徽记（提示未读消息 class=“badge”）

  ```html
  <a href="">超链接旁的未读消息<span class="badge"></span></a>
  
  <button class="btn btn-primary" type="button">
      按钮上的未读消息<span class="badge"></span>
  </button>
  ```

* 超大屏幕（class=“jumbotron”）

  ```html
  <div class="jumbotron">
      <div class="container" align="center"></div>
  </div>
  ```



* 正标题与副标题（class=“page-header”）

  ```html
  <div class="page-header">
      <h1>正标题<small>副标题</small></h1>
  </div>
  ```



* 略缩图

  ```html
  通过栅格布局放置图片
  ```

* 警告框（class=“alert alert-success”）

  ```html
  alert alert-success;
  alert alert-info;
  alert alert-warning;
  alert alert-danger;
  ```

  可关闭的警告框

  ```html
  <div class="alert alert-warning alert-dismissable">
      <button type="button" class="close" data-dismiss="alert" aria-label="Close">
          <span aria-label="true"&times;></span>
      </button>
      警告
  </div>
  ```

  警告框嵌套连接

  ```html
  <div class="alert alert-warning">
      <span>go to</span><a class="alert-link"></a>
  </div>
  ```



* 进度条

  ```html
  基本进度条 class="progress-bar"
  进度条颜色：
  progress-bar progress-bar-success|info|warning|danger
  进度条样式：
  progress-bar progress-bar-striped 带条纹
  progress-bar progress-bar-striped active 发廊动效
  堆叠式进度条：同一个div有多个进度条时
  <div class="progress">
      <div class="progress-bar" style="width:20%;">20%</div>
  </div>
  ```



* 列表组

  ```html
  <ul class="list-group">
      <li class="list-group-item">L1</li>
      <li class="list-group-item">L2</li>
  </ul>
  ```

  带徽记的列项

  ```html
  <li class="list-group-item">
      <span class="badge">12</span>
      L3
  </li>
  ```

  链接列表组

  ```html
  <div class="list-group">
      <a class="list-group-item active">A1</a>
      <a class="list-group-item">A2</a>
  </div>
  ```

  按钮列表组

  ```html
  <div class="list-group">
      <button type="button" class="list-group-item active">B1</button>
  </div>
  ```

  带背景色的列表项目

  ```html
  <div class="list-group">
      <a class="list-group-item list-group-item-success">A1</a>
  </div>
  ```



* 面板

  ```html
  <div class="panel panel-default">
      <div class="panel-heading">面板标题</div>
      <div class="panel-body">面板内容</div>
      <div class="panel-footer">面板脚注</div>
  </div>
  
  面板颜色：
  panel panel-default|success|info|warning|danger
  ```

* 嵌入效果（class=“well”）

  ```html
  <div class="well">文字呈现嵌入效果</div>
  ```



* 固定显示在顶部，不随网页下翻而消失

  ```html
  固定到顶部，不随网页翻动
  <nav class="navbar navbar-default navbar-fixed-top">
  	<botton class="btn btn-default">不会滚动的按钮</botton>
  </nav>
  
  固定到顶部，随网页翻动消失
  <nav class="navbar navbar-default navbar-static-top"></nav>
  
  固定到底部，不随网页翻动
  <nav class="navbar navbar-default navbar-fixed-bottom"></nav>
  ```

  

* 提示信息

  ```html
  非bootstrap工具提示:
  <botton type="button" title="非bootstrap提示"></botton>
  ```

  ```html
  bootstrap左侧提示：
  <botton type="button" data-toggle="tooltip" data-placement="left" title="提示信息">查看提示</botton>
  
  <script>
        $(function(){
            $("[data-toggle='tooltip']").tooltip();
        });
    </script>
  ```



* 弹出窗口

  静态窗口：dialog <- content :  header,title,body,footer

  ```html
  <div class="modal-dialog">
      <div class="modal-content">
          <div class="modal-header">标题
              <botton class="close" type="button" data-dismiss="modal"><span aria-hidden="true">x</span></botton>
          </div>
          <h2 class="modal-title">提示</h2>
          <div class="modal-body">文本框等</div>
          <div class="modal-footer">底部按钮等
              <button type="button" class="btn" data-dismiss="modal">关闭</button>
              <button type="button" class="btn">提交</button>
          </div>
      </div>
  </div>
  ```

  弹出式窗口

  ```html
  <button type="button" data-toggle="modal" data-target="#myModal">弹出</button>
  <div class="modal fade" id="myMosal" aria-labelledby="myModalLabel"><!--将modal fade 改为 modal就关闭动效，添加参数  data-backdrop="static"点击空白不会关闭-->
  	<div class="modal-dialog">
      ...
  	</div>
  </div>
  ```



