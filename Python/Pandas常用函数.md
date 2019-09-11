



------

- ##### Series ：带有索引的一维数组

  &nbsp;  &nbsp;  series具有name,dtype属性，name由系统分配，也可以pandas.Series.rename()重新定义name

  ```
  创建series
  >>> s = pd.Series(data,index = index)					
  >>> #data可以是dict，index=key，data=value
  >>> #ndarry，data=arr，index长度必须与arr一致（默认自动创建index=[0:len(arr)-1]）
  >>> #或标量（如5）,必须指定index
  ```

  &nbsp; &nbsp; series与ndarray类似，很多numpy函数对其有效

  &nbsp;  &nbsp; 将series转换成真正的ndarry使用函数,<font color='red'>series.to_numpy() </font>

  &nbsp;  &nbsp; series类似于dict，通过索引，修改和添加值

------

- #### DataFrame：带索引的二维数组

  &nbsp;  &nbsp;  具有index和columns属性，通过df.index  ,df.colums 访问

  ```
  创建DateFrame
  >>>df = pd.DataFrame(data,index=index,colums=colums)
  >>>#data可以是value为list、ndarry的dict，key=columns；
  >>>#series或dict的dict，key=columns
  >>>#dict的list，
  ```

- #### 对象创建

  1. series :   s = pd.Series(\[1,3,5,np.nan,6,8])

  2. DataFrame：创建以日期为索引 : dates = pd.date_range('20190228',periods=6)

     ```
     df = pd.DataFame(np.random.randn(6,4) , index = dates , colums = list['ABCD']
     
      Or : 通过dict创建
     ```

  ------

- #### 查看元素

```
df.head()      查看前5行数据，或指定行数
df.tail()      查看后5行数据，或指定
df.index       显示索引
df.columns     显示属性，df.columns.tolist() #将属性					输出为list
df.describe()  显示数据的快速统计摘要（各列 								count,min,mean等）
df.info()	   显示数据详情
df.T           转置
```

* #### 排序

```
df.sort_index(axis = 1, ascending=True)       将columns按大小排序 
df.sort_values(by = 'age',ascending = False) 将values按从大到小排序
```

  

------

- #### 选择

  df.\['age']  <==> df.age              选择age列产生Series

  df.\['colum_1' , 'column_2']       选择一个或多个列，产生Series

  df\[ 0:3 ]                                       选择0~2行

  df\['colum_0' : 'colum_3']          选择0~2列

  - ##### **按标签选取** loc() \ at()

  df.loc\[ : , \['colum_1'  , 'colum\_2']  ]  所有行的某些列

  df.loc\[ index_name \[ 0 ] ]                 第0行的所有列

  df.loc\[index_name \[ 0] , 'age']  Or df.at\[index_name\[0 ] , 'age']  年龄列的第0行的值

  - ##### **按位置选取(切片)** iloc() \ iat()

  df.iloc\[3]                  第2行

  df.iloc\[3:5 ,0:2]       3~4行&0~1列

  df.iloc\[ \[1,2,3]  , \[0,2] ]   通过list传入指定行和列

  df.iloc\[ : , 1:3 ]                 所有行，1~2列

  df.iat\[ 1 , 1 ]                     快速获取已知位置的值

  - ##### **boolean索引**

  df\[ df.age > 0]              age列 大于0 的所有信息

  df\[df >0]

  series.isin( list ) 方法 ：df\[ df.age.isin(\[ 22,18]) ]     age值等于22|18的所有信息 ！isin()必须传入list，标量无效

- ##### **设置、修改值**

  tips: 设置新列会根据索引自动对齐

  date = pd.date_range('20190228' , periods= len(df) -1)

	​		df['new_name'] = date

  df.at\[ index\[0] ,'age'] =0    第0行的age值改为0     标签方式

  df.iat\[0 ,1] = 0                      第0行&第1列的值改为0

  .to_numeric()	转float型

  .to_lsit               转列表

------

- #### 缺省值（NaN）

  df.info()  显示df详情

  **使用np.nan表示**

  - 删除缺省行数据

    df.dropna(how ='any') 

  - 填写缺失的数据

    df.fillna(value = 5)

  - 获取nan值所在Boolean

    pd.isna(DateFrame)

    pd.is_null(Series)

  ------

- #### 常用函数

  | 方法                  | 用途                              | 返回类型               |
  | --------------------- | --------------------------------- | ---------------------- |
  | pd.read_csv()         | 读取csv文件                       | dataframe              |
  | pd.concat()           | 合并pandas数据                    | series或dataframe      |
  |                       |                                   |                        |
  | series.isnull()       | series中是否有空值                | 同维度的Boolean series |
  | series.is_unique()    | series中是否有重复值              | Boolean                |
  | series.value_counts() | 统计series中所有取值出现  的次数  | 统计所得series         |
  |                       |                                   |                        |
  | df.info()             | 查看dataframe各属性类型，值的数量 |                        |
  | df.corr()             | 查看各属性相关性大小              |                        |
  | df.mean()             | 计算均值                          | series or dataframe    |
  | df.dropna()           | 删除nan数据                       | dataframe              |
  | df.drop_duplicates()  | 删除重复行                        | dataframe              |
  | df.head()             | 查看前5行                         | dataframe              |
  | df.tail()             | 查看后5行                         | dataframe              |

  ------

------



