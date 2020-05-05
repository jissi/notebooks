### JQuery

javascript框架，是对js的一种封装

---

* 需要第三方js库：jquery.min.js

  ```html
  导入第三方库
  <script src="path/jquery.min.js"></script>
  ```

* $(function(){})

  ```javascript
  $(function(){	//$()表示文档加载，function只有在文档加载之后才会执行
      //函数体
  });
  
  $(document).ready(function(){
      //同上,由 $(document).ready() 和function构成
  })
  ```

* 通过id获取元素

  与js的```document.getElementById(“id”)```不同；（DOM元素节点）

  JQuery使用 ```$(“#id”) ```获取（JQuery对象）；



* 添加监听器

  ```JavaScript
  增加click事件监听
  $("#id").click(function(){
      alter();
      //组成部分：$("#id").click() 和 function()
  })
  ```

  与原生js需要在html上监听事件不同，jquery不需要在html上进行操作，html只需要显示内容

---

* 常见方法

  ```JavaScript
  $("#id").val();//取值
  		.val(update)
  $("#id").html();//获取元素内容，如果有子元素，保留标签
  		.html(update);//更新元素内容
  $("#id").text();//获取元素内容，如果有子元素，去除子元素标签
  		.text(update);
  ```

* css样式操作

  ```JavaScript
  $("#id").addClass("class1");//为元素添加class名
  $("#id").removeClass("class1");//删除元素的class名
  $("#id").toggleClass("class1");//存在就删除，不存在就添加
  ```

  css函数，直接设置css样式

  ```JavaScript
  $("#id").css("样式属性"，"值")；//单个属性
  		 css({"属性1":"值1","属性2":"值2",...});//多属性
                   //属性和值都要用""
  ```

* 选择器

  选择器后的空格表示下一级元素

  ```JavaScript
  -----selector-----
  $("元素名");//选择所有该标签的元素
  $("#id");//根据id选择元素，重复id则只选第一个
  $(".类名");//选择类
  -----------------
  $("div#d1 span");//选中id为1的div内的span，层级选择
  	//注意空格，表示下一级
  $("selector:first|last");//满足selector的第一个|最后一个元素
  $("selector:odd|even");//满足selector的奇数|偶数元素，第一个下标为0
  $("selector:hidden|visible");//满足selector的隐藏|可见的元素
  
  属性选择
  $("selector[属性]");//满足选择器的有某属性的元素
  $("selector[attr='val']");//。。。属性="val"的元素
  $("selector[attr!='val']");//。。。属性不等于
  $("selector[attr^='val']");//。。。属性以val开头
  $("selector[attr$='val']");//。。。属性以val结尾
  $("selector*=val");//。。。属性包含val
  //一般使用[class='.className']
  
  表单对象选择器，选中form下会出现的输入元素
  $("selector:input");//所有输入元素：input,textarea,select,button等
  $(".form:input");//selector 为 .form ,选中form下的所有input
  :button		//选择type=button的Input和button元素
  :radio		//单选框
  :checkbox	//复选框
  :text		//文本框，不包含文本区
  :submit		//提交按钮，以及<buttom type="submit">
  :image		//图片型提交按钮
  :reset		//重置按钮
  
  表单对象属性
  :enabled	//可用的输入元素，输入元素默认可用
  :diabled	//不可用的输入元素
  :checked	//被选中的单选和复选
  :selected	//被选中的option元素
  
  $(this)当前元素
  ```

* 筛选器

  已经通过选择器选中的元素，进一步筛选

  ```JavaScript
  .first();//第一个
  .last();//最后一个
  .eq(num);//第几个,基0
  .parent();//最近的父元素
  .parents();//所有祖先元素
  .children();//紧接着的子元素
  .find(selector);//筛选出后代元素
  .siblings();//同级元素
  ```



* 属性

  ```JavaScript
  .attr("属性");//获取元素属性值，可以获取自定义属性，只能获取选中属性的初始值
  .prop();//获取元素，可以获取变化后的选中属性的值，并以bool返回
  //访问表单对象属性变化后的值应该使用prop
  .attr("属性"，"值");//修改属性值
  .removeAttr("属性");//删除属性
  ```



* 效果

  ```JavaScript
  .show(num);//显示,延时num
  .hide(num);
  .toggle(num);//切换显示或隐藏，延时num
  
  slideUp(num);//向上滑动
  slideDown(num);
  slideToggle(num);//滑动切换
  
  fadeIn(num);//淡入
  fadeOut(num);
  fadeToggle(num);//切换
  fadeTo(0.5);//指定淡入程度，取0-1的小数，0不淡入，1全都淡入
  
  animate({'right':'50px',top:'xxx'}，num);//自定义动画,右移50px,属性需要写在style中
  //html元素位置默认固定，需要通过css把元素的position设为relative|absolute|fixed
  //在animate的最后添加一个回调函数，效果结束后自动调用该函数
  animate({'top':'10px'},200,function(){
      alert("效果结束");
  })
  ```



---

* 事件

  ```javascript
  参数都是function
  
  页面加载
  $(function(){});//常用
  $(document).ready();
  load();//图片加载
  
  click();//单击，空格，回车
  dbclick();
  
  keydown();//按下，识别所有键，仅识别小写，不能识别最后一个
  keypress();//按住，不识别功能键，识别大小写，不能识别最后一个
  keyup();//弹起，获取所有输入字符
  通过event对象的which属性获取键盘按键
  
  mousedown();//按下
  mouseup();
  mousemove();//进入，每次移动都有效
  mouseenter();//进入,元素内移动无效,子元素有效
  mouseout();//离开,子元素有效
  mouseover();//进入,元素内移动无效，子元素无效
  mouseleave();//离开,子元素无效
  
  
  focus();//获取焦点
  blur();//失去焦点
  
  change();//内容改变，对于文本框，失去焦点才是改变
  submit();//提交form表单
  
  on("click",function(){});//绑定(点击)事件
  trigger("dbclick");//触发事件
  
  
  select操作
  $("option").attr("selected",false);
  $("select").find("option[value='"+type+"']").attr("selected",true);
  ```



---

* AJAX支持

  ```javascript
  $.ajax({
      type:'post',//get,put,delete
      url:page,//要访问的页面
      data:{"name":value},//参数
      dataType: "json",//服务器返回的数据类型
      success:function(result){}.//响应函数,result为服务器响应内容
      error:function(){}//请求失败回调函数
  });//提交ajax请求，使用参数集:$.ajax({...,...})
  
  $.get(
      page,//必须参数，其他参数可选
      {"name":value},
      function(result){}
  );//使用get方式提交ajax请求
  
  $.post(
      page,//必须参数
      {"name":value},
      function(result){}
  );
  
  
  $("#p").load(page,[data]);//调用ajax,id为要刷新文本的元素，page为服务器页面，data为提交的数据，可选
  
  $("#form").serialize();//格式化form下的输入数据
  	//form下输入的内容较多时，使用serialize()把输入数据格式化为字符串
  ```



----

* 数组操作

  ```JavaScript
  $.each(数组，fuction(index,val));//遍历数组，函数的第一个参数是索引，第二个参数用来存储值
  
  $.unique();//去重，执行去重前需要排序,函数的参数同上
  	list.sort();//排序
  	$.unique(list,function(i,n){})
  
  $.inArray(元素，数组);//返回元素在数组中的位置，不存在?-1
  ```

* 字符串操作

  ```JavaScript
  $.trim("str");//去除首位空白
  ```

* json

  ```javascript
  var json = "{\"name\":\"json\"}";
  $.parseJSON(json);//具有json结构的字符串转为json对象
  ```



---

* JQuery对象与DOM节点互转

  ```JavaScript
  get(0);或 [0]//JQuery转DOM
  $();//DOM转JQuery
  ```

  





-----

##### 使用笔记

* 设置全选与反选

  ```js
  $("#selectAll").is(":checked") ;//是否被勾选
  $(":checkbox").prop("checked",true);//设置全选
  $(":checkbox").prop("checked",false);//设置全部取消
  ```

  