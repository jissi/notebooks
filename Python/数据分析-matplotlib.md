## matplotlib库

---

```import matplotlib.pyplot as plt```

#### 1.Figure和Subplot

matplotlib的图像都位于Figure对象中

* ##### 创建figure对象

```python
fig = plt.figure()	#创建figure对象
	参数：
	figsize = () #图形尺寸
plt.figure(2)		#第几个figure
plt.gcf()			#获取当前figure尺寸
```

* ##### 创建子图subplot

```python
ax1 = fig.add_subplot(2,2,1)	#ax1为2行2列子图的第1个子图

	ax1 = fig.add_subplot(221)	#写法2
	ax1 = plt.subplot(221)		#写法3
plt.plot(.....)					#绘图
```

     <font color='red' size=3>创建一个figure对象，并返回一个含有创建的subplots对象的数组axes：	fig , axes = plt.subplots(2,3)</font>
```
subplots参数：
nrows=2			#行数
ncols=3			#列数
label='xxx'     #用于创建图例
sharex=True		#使用相同的x轴刻度
sharey=False	#使用相同的y轴刻度
subplot_kw=		#用于创建subplots的字典
```
​	用法:

```
fig , axes = plt.subplots(2,2,sharex=Ture,sharey=True)
for i in range(2):
    for j in range(2):
        axes[i,j].plot(...)
```

* ##### 调整subplots的间距<font color='red'> plt.subplots_adjust()</font>
```
    plt.subplots_djust()
    参数
    left=None,right=None		#左右
    bottom=None,top=None		#上下
    wspace=None,hspace=None		#宽高
```

#### 2. 刻度，标签和图例

```
#有参数时设置，没有参数时返回当前设置
plt.xlim()			#x轴范围
plt.xticks([刻度点],[刻度名])		#x轴刻度位置
```

* ##### 设置标题、轴标签、刻度以及刻度标签
```
ax.set_xticks([1,10])				#刻度点
ax.set_xticklabels(['one','ten'])	#刻度名
ax.set_xlabel('x轴名')			   #x轴名
```

* ##### 绘制图例

```
ax.legend(loc='best')  #图例位置
plt.legend()
方法1：
在创建subplot时加入label=‘xxx’ 参数
ax.legend()
方法2：
ax.legned( ['label_1','label_2'] )
```

* ##### 注解

```
   ax.text(x,y,'str',fontsize=10)
   		#在(x,y)出显示str
   ax.arrow()
   ax.annotate()
```

---



#### 3. 绘制图形

##### 图形对象被称为块（patch）

```
circ = plt.Circle( options )	#创建圆形path对象
rect = plt.Rectangle(   )		#创建正方形
pgon = plt.Ploygon( )			#创建三角形
ax.add_patch(circ)
```

---

#### 4. 保存图形到文件

##### 保存到磁盘

```plt.savefig('path.svg')	#扩展名自行定义```

##### 也可以将图形保存到其他任何文件型对象，常用于web生成动态图形

Figure.savefig()的参数

```
fname=xxx.png				#文件名
dpi=[100]					#分辨率
facecolor,edgecolot			#背景色，默认w白色
format='png|pdf|svg|ps...'	#文件格式
bbox_inches='...'			#图表要保留的部分，‘tight’：剪去周边空白
```

---

#### 5. Pandas绘图

​	<font color='blue' size=4>使用方法：</font>

```
1. 手动调用
	import matplotlib.pyplot as plt
	plt.plot( Series )
2. ipython
	%matplotlib inline
	Series.plot()
```

<font size=3 color='green'>线形图	kind='line'</font>

  * ##### Series对象默认index作为x

  * ##### DataFrame对象默认index作为x，columns作为label，并自动绘制图例

    Series.plot( )参数

```
label=[]			#创建图例使用到的标签
ax = ax1	   		#使用的子图（事先定义）
style='ko--'		#绘图风格
alpha= 0.5			#透明度 0-1
kind='line|bar|barch|kde'		#图形样式
logy=True			#y轴使用对数
use_index=True		#使用Series的index做x
rot=30				#旋转刻度标签（0-360）
xticks=[0,1,..]		#x轴刻度
ylim=[0,10]			#y轴界限
grid=Ture			#网格线	
```

​	DataFrame.plot( )参数

```
subplots=[ax1，ax2...]		#将各个df列绘制到子图
sort_columns=False			#以字母顺序绘制个列
sharex=True
sharey=True
legend=True
figsize=(,)					#图像大小元组
title=''					#图像标题
```

<font size=3 color='green'>柱状图	kind='bar'、kind='barh'</font>

* ##### Series的index作为x(barh时为y)

* ##### Dataframe的index作为x(barh时为y)，每行的columns值为一组，设置<font color='red'>stacked=True呈现堆积状</font>，列名称（df.name）作为图标题

```利用s.value_counts().plot(kind='bar')显示每个值的频率```

<font color='green'>直方图（hist）和密度图（kde）</font>

* ##### hist的横轴表示数值，纵轴表示该数出现的次数

* ##### kde的横轴表示数值，图形表示数值的密度分布

<font color='green'>散点图（scatter）</font>

```plt.scatter(x,y,color='k',alpha=0.5)```

```plt.scatter(df)	#绘制散布图```