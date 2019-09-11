### HTML

中文乱码问题

```html
<head>
    <meta http-equiv="Content-Type" content="text/html;charset=UTF-8">
</head>
<!--meta是元标签，http-equiv类似于http头部协议，content是内容类型-->
```

标签：由开始标签和解释标签组成

元素：开始标签到结束标签之间的所有内容

​			特殊元素：\<hr/>：水平线；\<br/>：换行

属性：修饰标签，属性名=属性值

\<h1 align="center">居中</h1>

注释：```<!-- 注释 -->```

---

### 基本元素

```html
<h6>h1标签内自动粗体，并换行，123456字体依次变小</h6>
<p>文本自动换行</p>
<b>b是bold的缩写，粗体</b>
<strong>粗体，语义加重</strong>
<i>i是italic的缩写，斜体</i>
<em>斜体，em是Emphasized的缩写，语义加重</em>
<pre>预格式java代码</pre>
<del>删除效果线</del>
<ins>文字带有下划线</ins>
```

```html
图像（img）
<!--img标签不能自己居中，需要放在能够居中的标签中，h1,p,div-->
<div align="center">
    <img src="图片路径,绝对路径file://path" width="200" 		height="200" alt="图片缺失时显示的文字"/> 
</div>
```

```html
超链接（a）
<a href="网址">超链文字，点击跳转</a>
属性：
	target="_blank" 新页面打开超链接
	title="提示文字" 鼠标指向超链文字是显示提示文字   
```

```html
表格（table）
table 表格
tr(table row) 行
td(table data) 列，单元格

table属性：
	border="1" 表格边框1px
	width="200px" 表格宽200px
td属性：
	width="30px" 列宽30px，同列自动继承
	width=80%
	align="left" 单元格水平对齐
	valign="center" 单元格垂直对齐
	colspan="2"	占用两列
	rowspan="2" 占用两行
	bgcolor="red" 背景色
```

```html
列表（ul或ol）
有序列表：ol
无序列表：ul
<li>某项</li>
```

```html
块（div或span）
div和span都是布局用的，本身没有显示效果
div是块元素，自动换行，h1,table,p也是块
span是内联元素，不会换行，img,a,b,strong也是内联元素
```

```html
字体（font）
<font>字体设置</font>
属性：
	color="red"
	size="2"
```

```html
内联框架（iframe）：实现在网页中插入网页
相当于一个子浏览器
<iframe src="网址" width="600px" height="400px"></iframe>
```

---

<font color="red">attention</font>

```html
文本框（input,type="text"）
	文本<input type="text" size="10" value="初始值" 		placeholder="背景文字" name="数据名称">

密码框（input,type="password"）
	密码<input type="password" >

单选框（input,type="radio"）
	选项1<input type="radio" checked="checked"       name="choose1">默认选中
	选项2<input type="radio" name="choose1">
实现相同的name属性值，将同一name的单选框分为一组,只能选一个

复选框（input,type="checkbox"）
	同单选框，使用相同name属性分为一组,多个同组checkbox构成复选框

普通按钮（input,type="button"）
	不能具备提交form的功能
	<input type="button" value="显示按钮名">

提交按钮（input,type="submit"）
	用于提交form到服务器
	<input type="submit" value="显示按钮名">

重置按钮（input,type="reset"）
	把输入框复原
	<input type="reset" value="重置">

图像提交按钮（input，type="image"）
	提交form
	<input type="image" src="path">
```

```html
表单（form）向服务器提交数据
<form action="把数据提交到哪"></form>
属性：
	method="post|get" 提交数据的方式，默认get
				get方法会在浏览器地址栏显示提交的参数
```

```html
下拉列表（select）
select需要配合option使用

<select size="2" multiple="multiple"><!--设置高度，多选-->
     <option selected="selected">选项1</option><!--默认选中-->
    <option>选项2</option>
</select>
```

```html
文本域（textarea）
可以有多行，有滚动条
<textarea cols="宽度30" row="行数8"></textarea>
```

```html
按钮标签（button）
	与<input type="button">不同，button标签功能更丰富
	<button>按钮显示的文字</button>
	<button><img src="path"/></button><!--图片按钮-->
	<button type="submit">提交</button><!--提交按钮-->
```



