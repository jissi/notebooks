## Pandas 基本功能

1. #### 重新索引（reindex）

   对于series：重新排序，不存在值则为nan

   对于时间序列，有利于插入新值

   ```
   obj.reindex(索引列表 , method='ffill/bfill')向前/向后搬运
   ```

   对于dataframe：可以修改index或column，（不能修改名称）

   ```
   用columns关键字重新索引列
   df.reindex(columns=[属性列表])
   df.rename(columns={'col_1':'col_'}) #重命名索引
   使用ix标签索引使得重新索引更简洁：
     df.ix[[index列表]，columns=[属性列表]]
   ```

   <font align='center' >`reindex函数的参数`</font>

   | 参数       | 作用                                                 |
   | ---------- | ---------------------------------------------------- |
   | index      | 用于替换的新索引                                     |
   | method     | 插值方式                                             |
   | fill_value | 缺省值                                               |
   | limit      | 向前/后填充的最大填充量                              |
   | level      | 在multiindex的指定级别上比配简单索引，否则选取其子集 |
   | copy       | 默认true                                             |

   

2. #### 丢弃指定轴上的项（drop）

   对于Series：obj.drop(索引列表)

   对于dataframe：``` df.drop(列表，axis=0|1) 删除索引或属性```

   

3. #### 索引、选取和过滤

   series的索引与numpy类似，但索引不只是整数

   标签的切片包含末端

   ```
   df.ix[index列表，columns列表 ] 用标签索引
   ```

   修改标签值：```df['a','b'] = 0```

   

4. #### 算数运算和数据对齐

   自动填写缺省值为nan

   对象相加会取并集（both index and columns）

   

5. #### 在算术方法中填充值

   ```df1.add( df2 , fill_value=0)``` #两对象相加，没有重叠的部分填充0

    fill_value关键字同样适用于sub（-）、div（/）、mul（*）、reindex

   

6. #### dataframe与series之间的运算

   广播机制：将series的索引匹配到dataframe的列，并沿着行一直向下广播（使用算术运算符）

   使用axis关键字匹配行在列上广播（使用算术运算函数）

   

7. #### 函数应用与映射

   * numpy的方法用来操作pandas对象（eg：np.abs(df)）

   * python方法也可用：

     eg ：

     ```
     format = lamda s: '%.2f' %x
     df.applymap(format)
     # series.map(format)
     ```

     

   * df.apply(function , axis=0|1)将函数应用到各行或列上的一维数组

     

8. #### 排序与排名

   - 按索引排序 <font color = "red">series.sort_index()</font>

   ​      对于dataframe可以选择任意轴上的索引排序 

   ```
   df.sort_index(axis=1)
   排序关键字：ascending=True/False 是否升序排列，默认True
   ```
   
* 按值排序
  
  series.oder()   缺省值排到最后
  
  df.sort_index(by=‘column_1’)   dataframe中将n个列名传给by关键字即可按该列数值排序
  
     
  
9. #### 排名（rank)

   ```.rank([ascending=False| True] [,method='average | min | max | first '])```

   

10. #### 索引值重复

    查询索引的 is_unique属性：```df.index.is_unique```

    查询数据中有重复索引值则会返回series

    

11. #### <font color='red' size=4>汇总和计算描述统计</font>

    ```
    df.sum(axis=0|1)  #对所有行|列求和
    		#默认忽略nan值(skipna=True)
    df.describe()     #一次产生多个汇总
    percentiles=[.20,.40,...]#百分比关键值，定义要显示的百分比			
    df.count()		#统计非nan值
    min、max			#计算最小最大值
    argmin、argmax	#最小最大值的位置
    mean			#计算平均值
    median			#计算中位数
    var				#方差
    std				#标准差
    ```

    

12. #### 相关系数与协方差

    ```
    series.corr() #计算两个series中重叠的非nan的按索引对齐的值的相关系数
    series.cov()  #计算协方差
    df|series.withcorr(df|series,axis=0|1)  #计算行|列与另一个df或series的相关系数
    ```

    

13. 唯一值、值计算以及成员资格

    ```
    1.series.unique()  #求唯一值
    （series.unique.sort() #进行排序）
    
    2.series.value_counts() #计算各个值出现的次数(默认降序排列)
    pandas.vaule_counts(series.values,sort=false)
    
    3.obj.isin([value1,value2]) #判断obj中是否有所提供的值，返回布尔型数组
    ```
    
    

14. #### <font color='red' size=4>处理缺失数据</font>

    * pandas 统计函数都排除了缺失数据

    * ``` df.info() #查看数据缺失```

      ```
      1. obj.isnull()  #判断df|series中是否有nan，返回布尔型df或数组
         obj.notnull() #判断obj中是否有非nan，返回布尔型df或数组
      
      2. obj.dropna()  #删除nan
      3. obj.fillna(vaule=5)  #填充nan
      ```
      

    

15. #### 滤除缺失数据

    ```
    1. obj.dropna(how='all'，axis=0)  #删除  nan;，默认删除整行
    2.obj[obj.notnull()]
    ```

    

16. #### 填充缺失数据

    一般填充mean() ,median()

    ```
    1. df.fillna(0)   #填充为0
    2. df.fillna({'column1':value1, ...}) 
       #对不同列填充不同值
    3. 关键字参数：
       inplace=True   #在现有对象上修改，默认            					False（返回新对象）
       method='ffill|bfill'  # method同reindex
       axis=0|1   #默认0
       limit=5   #最大填充量
    ```
    
 
    
17. #### 层次化索引

    层次化索引使一个轴上有多个索引级别

    （降维数据）

    生成多级索引：pd.MultiIndex.from_arrays()

    ```
    #创建多级索引并指定索引名
    col = pd.MultiIndex.from_arrays( [ list1,list2 ],names=['name1','name2'])
    #创建多级索引
    indx = [[list1],[list2]]
    pd.DataFrame(data,columns=col,index=indx)
    
    #指定各级索引名
    df.index.names = ['name1','name2']
    ```

    ```
    df = series.unstack()  #生成dataframe
     s = series.unstack().stack()  #unstack的逆运算
    ```

    对于dataframe，每条轴都可以有分层索引

    （索引的索引，属性的属性）

    ```
    对于df的多层索引，可以指定索引名
    df.index.names=['name1','name2']
    df.columns.names=['column1','column2']
    ```

    

18. #### 重排分级顺序

    调整索引级别

    ```
    df.swaplevel('index2','index1') #重排索引
    df.sortlevel(1) #根据单个级别索引对数据排序
    ```

    

19. #### 根据级别汇总统计

    ```
    df.sum(level='index1',axis=0|1) #分组求和
    ```

20. #### 使用dataframe的列

* <font color='#f0c9cf'>将column变成index</font>

  ``` df.set_index(['column1'],drop=False)```

   drop=True 默认不保留做索引的column

* <font color='#f0c9cf'>将index变成column</font>

  ``` df.reset_index(['index1']) ```
  
  

21. #### 其他有关pandas

* 整数索引

  在pandas中使用python列表的某些切片方法可能报错 如```series[-1]```

* 面板数据（Panel）

  三维版的dataframe

---

## 数据加载、存储与文件格式

1. #### pandas中的解析函数

   <font color='#fed71a'>常用：read_csv ; read_table</font>

   ```
   1. pd.read_csv('path')	#从文件，url文件型对象读取					带分隔符的数据，默认分隔符为逗号
   		sep=',' 设定分隔符，可用re
   		names=['column1'] 指定列名
   		header=None		指定列名的行，默认分配列名
   		index_col='column1' 指定某列为索引
   		index_col=['index1','index2'] 指定多重索										引
   		encoding='utf-8'
   		thousands='.'   #千分位分隔符
   		nrows=5		#
   		
   		
   2. pd.read_table('path') #默认分隔符为制表符“\t”
   3. pd.read_fwf()		#读取定宽列格式数据(无分隔							符)
   4. pd.read_clipboard()	#读取剪贴板中的数据
   ```

2. #### 将数据写出到文本格式

   ```
   df.to_csv('file_name')	#将数据写到csv文件								(sep=',')
   			sep=''	#指定分隔符
   			na_rep='NULL'	#指定填充缺失值
   							默认空格
                index=False	#不写index
                header=False	#不写列名
                cols=['col1',...]#写部分列名
   ```

   

3. #### json数据

   * <font color='red' size=4>json 数据与pytho数据的相互转换</font>

     ```
     import json
     obj = '''....'''  #json数据
     result = json.loads(obj)  #json --> python
     result = json.load(open('path'))
     asjson = json.dumps(result) #python --> json
     ```

-----

# 数据规整化：清理，转换，合并，重塑

1. #### 数据库风格的df合并

   ```
pd.merge(df1,df2)  #默认重复列名作为键,取交集
   		-----------------------------
   		【left=】df1	#参与合并的左侧df
   		sort=True
		how='inner|outer|left|right'  
   			# inner取交集
   			# outer取并集
   			# left 保留左侧df(在左侧df的基础上取交集)
   	指定外键-------------------------------------
   		on='column'	#连接的列名
   		left_on='column' #指定左边df的外键
   		right_index=False#将左侧df的索引作为外键
   
   		
   ```

2. #### pd.concat()

   ``` 
   pd.concat([series1,serises2],axis=0) #concat默认在行上合并
   参数------------------------------------------
   	join='inner| ...'
   	axis=0|1
   	join_axes=[['a','b','c']]  #指定合并时使用的外键
   ```

3. #### 合并重叠数据（索引重叠的两组数据）
	```
	 df1.combine_first(df2)  #用df2给df1打补丁
	 			（以调用数据为基础，从参数数据中更新数据			（nan值除外））
	```

4. #### 重塑层次化索引

   ```
   df.stack()	#将列转为行  df-->series
   df.unstack()	#将行转为列 series--> df
   ```

   

5. #### 数据长格式（时间序列等）转宽格式

   ```df.pivot('做索引的col','做col的col','做值的col')```

6. #### <font color='red'>移除重复数据</font>

   ```
   df.duplicated()     #返回一个是否重复的bool Series
   df.drop_duplicates() #返回去掉重复后的df,保留最先出现的值
   df.drop_duplicates(['column1']，take_last=True) #针对某列去重,保留最后						出现的值
   ```

7. #### df.map(函数)

8. #### <font color='red'>替换值</font>

   <font color='red'>!! ! ! 使用jupyter时会因为缓存问问题导致替换后值为nan</font>

``` 
df.replace([待替换的值1，待替换的值2]，[替换值1，替换值2])  
df.replace({待替换值：替换值，...})
df.col.map(dict|series|function) #常用于修改单个序列的值，支持字典

对比
df.col.map()  #针对单列操作
df.apply()    #针对多个列的操作
df.applymap() #针对df中的每个值操作
```

9. #### 重命名轴索引

   ```
   eg:
   	df.index.map(str.lower)
   	df.rename(index=str.lower,columns=str.upper,inplace=False)
   	df.remane(index={'sds':SDS,...},columns=...)
   ```
   

   
10. #### 字符串对象方法

    ```
    .split(',')
    .strip()  #结合split，去掉空格
    ```

11. #### 正则表达式

    ```
    import re
    regex = re.compile('\s+')	#编译一个re对象
    ```

    ​	详见正则

12. #### pandas作图

    ​	 见matplotlib库
    
    

---
# 数据聚合与分组运算
* ### Group by

``` 
df.['col_0'].groupby(df['col_1'])

	as_index=False 参数可以取消使用分组作为索引
	group_keys=False  禁用分组键，以免产生层次索引
df.groupby( df['col_1'] , df['col_2'] )  <==> df.groupby( ['ky1','key2'] )
df.groupby('key1').size()    返回分组大小
```

##### 分组键要求：

​		与轴长度一致的  列表或数组（包括：df['col'] 、

​							字典或Series  给出待分组轴上的值与分组名之间的      对应关系  、

​							函数                 用于处理轴索引或索引中的各个标签 ）



##### groupby 依据在axis=0 （行数值）分组，也可以按axis=1 列数值分组：

```
eg: 根据各个列的数值类型分组
df.groupby(df.dtype,axis=1)
```



1. #### 对分组进行迭代

   ##### groupby对象迭代产生一组二元数组   ：分组名 ， 数据块

   ```
   for name , group in df.groupby('key1'):
   			print(name)
   			print(group)
   			
   对于多重键的情况（多重分组）
   分组名有多个，应该写成多个键组成的元组，任然使用一个name也可以。
   for (k1,k2) , group in df.groupby(['key1','key2']):
   			print(k1,k2)
   			print(group)
   ```

   ##### groupby对象转成字典

   ```dict(list(df.groupby('key1')))```

   

2. #### 选取一个或一组分组

   ```
   df.groupby( df['key1'] ) ['data1']					#选取一列
   df.groupby( df['key1'] ) [ ['data1','data2'] ]		#选取多列
   
   语法糖写法
   df['data1']            .groupby(df['key1'])
   df[['data1','data2']]  .groupby(df['key1'])
   ```

   

3. #### 通过字典，series，函数进行分组

   * ##### 通过字典分组

     使用字典将df的columns映射为分组名,<font color='red'>位置要与原cols对应</font>

     如

     ```
     df.columns  的值为： a ,b ,c ,d
     g = {'a':'red','b':'red','c':'bule','d':'red'}
     df.groupby(g,axis=1)
     ```

     

   * ##### 通过series分组

     series被看做一个固定大小的映射，修改其索引值为对应col值即可，

     ```
     s = pd.Series(g)
     df.groupby(g,axis=1)
     ```

     

   * ##### 通过函数分组

     函数会在各个index上调用

     ```
     df.index > 'aa','aaa','bb','bbb','cc'
     df.groupby(len)				#对各个index值调用len函数，按index值的长度分组
     ```

     

   * ##### 根据索引级别分组

     通过level关键字传入级别等级或级别名

     ```
     df.groupby(level='level_1')
     ```

     

 

* ### 数据聚合
	
	groupby 对象使用聚合函数
	
	<center>经过优化的groupby的方法</center>
	
	| 函数名      | 说明                    |
	| ----------- | ----------------------- |
	| count（）   | 分组中非nan值的数量     |
	| sum（）     | 非nan值的和             |
	| mean（）    | 非nan值的平均值         |
	| std、var    | 标准差和方差            |
	| min、max    | 非nan的最小、最小值     |
	| prod        | 非nan值的积             |
	| first、last | 第一个和最后一个非nan值 |
	
	
#### 面向列的多函数应用

<font color='red'>	groupby.agg('函数名')</font>

​	运算得到的列以函数名命名

     ```
groupby对象.agg(['function_1'，'function_2'...])  
groupby对象.agg([('列名','函数名')，('列名','函数名')，...])  #指定列名

应用于所有列或多个列的函数
groupby对象['col1','col2',...] .agg(['count','mean','max',...])
		#结果是层次化索引，[ ['col1','col2', ...] , ['count','mean',..]]
		也可以在agg（）传入('列名'，'函数名')来指定列名

对不同列应用不同的函数
	在agg()传入 列名：函数	的映射
	function = { 'col1':'mean','col2':['mean','max',...],..}
	...agg(function)
只有将多个函数应用到至少一列时才会有层次索引
     ```



* ### 分组级运算和转换

  使用groupby对象.transform(函数)，将一个函数应用到各个分组的值

  <center><font color='red'>使用.transform() 和直接使用.函数的区别</font></center>

      ```
  例如，同样使用mean() 函数
  
  groupby_obj.mean()
  	是先将数据分组，在分组的基础上求mean
  	即，只给出 某个分组的平均值
  	输出结果为 
  		a 12
  		b 2
  	a，b为索引
  groupby_obj.transform(np.mean)
  	是直接在属于该分组的值的位置写上该分组的mean
  	即，给出每行记录所属分组的平均值
  	输出结果为
  		0  12
  		1  12
  		2  2
  	0,1,2为索引
      ```

#####                    距平化函数：

​				def demean(arr):

​							return arr - arr.mean()



* ### 透视表和交叉表

  * #### .apply(function) :拆分-应用函数-合并

    ```python
    eg：
    
    df = pd.DataFrame({'key1':['a','a','b','b','a'],
                      'key2':['one','two','one','two','one'],
                      'data1':range(5),
                      'data2':range(5,10,1)})
                      
    	def top(df,n=1,column='data1'):
        return df.sort_index(by=column)[-n:]
        
    df.groupby(df.key1).apply(top)
    
    out：
    		key1	key2	data1	data2
    key1					
    a	4	a	one	4	9
    b	3	b	two	3	8
    ```

    ```python
    groupby_obj.describe() <==>
    f = lambda x: x.describe()
    groupby_obj.apply(f)
    ```

    

  * #### 分位数和桶分析

    划分元面(bin)<font color='red'>pd.cut()</font>

    ```python
    ages = [20,22,25,27,...]
    要将ages划分为 (18,25],(26,35]...等几个元面
    bins = [18,25,26,35,...]
    cats = pd.cut(ages,bins)
    返回categorical对象：
    	
    ```

    

  * 透视表
  
    .pivot_table()
  
    ```
    参数
     values=要聚合的列名，默认所有列
     rows=用于分组的列名或其他分组键，透视结果的行
     cols=由于分组的列名或其他分组键，透视结果的列
     aggfunc=聚合函数列表，默认‘mean’
     fill_value=要个nan填充的值
     margins=False 添加行、列小计的总计，默认false
    ```
  
    
  
  * 交叉表
  
    pd.crosstab(分组键，列名，margins=True)