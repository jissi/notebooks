# numpy

<h6>import numpy as np </h6>

---
* ***创建numpy数组,生成ndarray对象***
```
  np.array( [[1,2,3] , [4,5,6]] )
  
  np.arange(start , end , step) #只有一个参数时该参数为end
  np.linspace(1, 3, 5)   #生成\[1,3)的5个数组成的一维数组
  
  np.zeros(5) 			 #生成5个0组成的一维数组
  np.zeros( (2,3) )		 #生成2行3列的值为0的二维数组
  np.ones( (2,3) ) 		 #生成2行3列的值为1的二维数组
  
  np.eye(6) 	#生成6行6列的恒等矩阵（对角线为1，其他值为0）
  
  np.random.rand( 3 ) 	#随机0-1的三个数组成数组
  np.random.rand(2,3)	#
  np.random.randn() 	#以0为中心的正态分布
  np.random.randn(2,3) 	#(2,3)正态分布
  np.random.randint( )  #随机生成整数
  np.random.randint( 2,20,8,size=(4,2)) #[2,20)间随机8个数生成(4,2)数组

  
  np.tile(object , (2,2) ) #将object扩展2行2列二维数组
  
  np.newaxis 			   #添加维度
```
---
* ***对ndarray对象的操作***
```
arr = np.array([[1,2,3],[4,5,6]])
arr.shape#查看数组行列数
arr.ndim #查看数据维度
arr.dtype #查看数据类型
arr.reshape(2,2) #将数组改为2行2列

arr.reshape(-1,1)  # -1代表任意整数，主要看非-1的位置

arr.max()  #查看最大值
arr.min()
arr.argmax(axis=0/1) #定位列/行最大值索引
arr.argmin(axis=0)
arr_tile = np.tile(arr,(2,2))#将数组arr扩展为2行2列
arr_sort = np.sort(arr ,axis=0) # 将数组arr按列排序
	arr.cumsum()	#累计求和
[1,2,3].cumsum() = [1,3,6]
```

* ***索引，与python类似***
 1. 一维数组：
 arr[ 8 ]  ；arr[2 : 6] ; arr[ : 6] ; arr[2 : ]
 2. 二维数组：
    arr[1][1] <==> arr[1,1] #选取第一行第一列
    arr[ : 1] #选取第一列所有数
    arr[ : 1 , : 2] #选取前一行的前两列数
---
* ***广播机制***
修改某个值：arr[0:3] = 10 #让前三个数为10
---
* ***运算***
***1.*** **布尔运算:**
& ，| ，> ,< ,== 
***2.*** **算数运算:**
+ ，- ，* , / 
叉乘 arr_1 ==.dot==(arr_2)
***3.*** **求和:**
arr.sum() #所有元素和
arr.sum(axis = 0) #求所有列的和
***4.*** **其他函数**
np.pi  #π
np.sin() 、cos() 、tan()
np.sqrt() #平方根
np.square() #平方
np.log()
np.exp #指数值
np.std()
---
* ***数组的复制***
a = array([1,2,3])
b = a #a,b的ID仍然相同，修改b，a也会随之变化
c =a.view() #c,a不相等，shape不相关，但元素仍然共用
d = a.copy() # d,a相互独立