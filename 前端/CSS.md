CSS

分层设计，css将颜色，大小，位置等信息都写到\<style>中，html只需关心提供什么样的内容

​	**css可以写在html的head的style标签内**

​	**css也可以存在于单独的一个文件（不需要在style内），html包含该文件：**

```html
<link rel="stylesheet" type="text/css" href="path" />
```

##### 优先级问题

```css
外部css文件与html内style标签冲突，优先使用最后出现的样式；
style标签与<p style:></p>style属性冲突，优先使用属性
样式上带有 !important 则优先级最高，高于style属性eg.
p{
    color:red !important;
}
```



* css语法

  ```css
  selector {property:value}
  选择器 {属性：值}
  ```

  ```标签{ 属性：值}```与直接在标签上加```style=”属性：值“```效果相同

* 选择器（三种）

  ```css
  元素(标签)选择器{属性：值}
  #id选择器{属性：值}
  .类选择器{属性：值}
  
  元素名.类名{属性：值}
  ```

* 注释

  ```css
  /* 注释*/
  ```

---

* 背景


```css

<style>
p{
    /*尺寸和背景色*/
    width:50%;
    htight:50px; 
    background-color:red;			/*背景色*/
    background-image:url(path);		/*背景图片*/
    background-repeat:repeat;		/*背景重复*/
    	/*repeat:水平垂直重复
    	  repeat-x:水平重复
    	  repeat-y:垂直重复
    	  no-repeat:不重复*/
    background-size:contain;	/*背景平铺，拉伸，会失真*/
    
}
</style>
```

* 文本

  ```css
  <style>
  div{
      color:red|rgb()|16进制颜色;
      text-align:left|right|center; /*文本对齐*/
      text-decoration:none;		  /*文本修饰*/
      	/*overline:上划线
      	  underline:下划线
      	  line-through:删除线
      	  none:无
      	  blinl:闪烁
      	*/
      line-height:50%|10;		/*行间距*/
      letter-spacing:2;		/*字符间距*/
      word-spacing:10;		/*词间距*/
      text-indent:50;			/*首行缩进*/
      text-transform:uppercase;/*大小写*/
      	/*uppercase:全部大写
      	  lowercase:全部小写
      	  capitalize:首字母大写
      	*/
      white-space:normal;空格设置
      	/*noraml:默认多个空格或换行被合并为一个空格
      	  pre:显示所有空格
      	  pre-wrap:显示所有空格，超出父容器长度自动换行
      	  nowrap:不换行直到使用<br/>
      	*/
      word-wrap: normal;
      		/*break-word 超出换行
  }
  </style>
  ```

* 字体样式

  ```css
  font-size:20px|50%
  font-style:normal（标准）|italic（斜体）
  font-weight:normal(标准)|bold(加粗)
  font-family:"字体"
  /*合并大小，风格，粗细，字体*/
  font:italic bold 30px "宋体";
  ```

* 鼠标样式

  ```css
  cursor:crosshair;十字架
  		default:
  		auto
  		pointer
  ....
  ```

* 表格

  ```css
  table-layout:automatic;单元格大小由td内容决定
  			 fixed:单元格大小由td上设置的宽度决定
  border-collapse:separate;边框分离
  				collapse；边框合并
  ```

* 边框

  ```css
  boder-style:solid(实线)
              dotted(点线)|dashed(虚线)|double(双线)
  border-color:red|rgb()|#ff0000
  border-width:5px
  /*上下左右边框的style，color，width*/
  border-top[|left|right|bottom]-style:solid
  /*将颜色，风格，宽度合并*/
  border:1px dotted Blue;
  ```

  块元素（div,h,p等）占用100%宽度

  内联元素(span,a,b,strong,i,input等)占用宽度由内容决定

* 内边距（元素内容与元素边框的距离）

  ```css
  padding-left:10px;左内边距
  padding-right,top,bottom
  padding:10;四个方向均为10
  padding:10 20 30 40;顺时针各方向值
  ```

* 外边距（元素边框和元素边框之间的距离）

  ```css
  margin-left,right,top,tottom
  ```

* 超链接状态

  ```css
  超链接四种状态：
  	link 初始状态，从未被访问过
  	visited 已访问过
  	hover 鼠标悬停在超链接上
  	active 鼠标按下左键，尚未松开
  伪类：被选中的元素处于某种状态
  <style>
  a:link {color:white}
  a:visited {color:blue}
  </style>
  去除超链接下划线
  a{
      text-decoration:none
  }
  ```

* 隐藏元素

  ```css
  display:none;隐藏该元素，让出所占空间
  visibility:hidden;隐藏元素，该元素所在位置变为空白
  ```

----

##### 布局

* 绝对定位（参照最近的一个定位过的父容器）

  ```css
  position:absolute;
  left:10px;
  right,top,bottom
  z-index:1;/*覆盖，值大的在上面*/
  ```

  

* 相对定位（在原来位置上移动）

  ```css
  position:relative;
  ```

* 浮动（浮动的框左右移动，直到外边框碰到其他边框）

  ```css
  float:left|right
  clear:left|right;/*不允许浮动*/
  图片浮动时，文字自动环绕图片
  div默认换行，使用了float后就会水平排列，直到碰到父容器边框
  ```

* 元素的display显示方式

  ```css
  隐藏，块，内联，内联-块
  display:none;/*隐藏，原位置变空白*/
  display:block;/*以块显示，span设置后可以自动换行，    width,height生效*/
  		inline;/*内联，没有换行，width,height无效*/
  		inline-block;/*内联-块，不换行且能指定大小*/
  ```

* 水平居中

  ```css
  <div align="center">div内容居中</div>
  <div style="text-align:center">div内容居中</div>
  <div style="width=30px margin:0 auto">div居中</div>
  span需要嵌套在div内部，通过div实现居中
  ```

* 垂直居中

  ```css
  line-height:10px;/*适合单独一行垂直居中*/
  padding:30 0 ;/*借助相同的上下边距，实现多行文本居中*/
  table方式居中：
  	<div style="display:table-cell"></div>;把div用单元格显示
  	<td style="vertical-align:middle"></td>;单元格居中
  ```

* 多个span之间有空格

  ```css
  解决办法：span使用float，所有span结束后使用
  		<div style="clear:both"></div>使后续元素不会和span重复在一起
  ```

  