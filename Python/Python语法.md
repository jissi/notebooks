<center>Python语法</center>

初衷：xxx

[TOC]

---

### 1. 变量和基本数据类型

#### python文件命名规则：simple_python.py

#### 变量命名规则：

1. 可以包含 字母，数字，下划线
2. 可以字母、下划线开头，但是不能数字开头
3. 不能与关键字冲突
4. 慎用1 l 和 0 o

#### 字符串

```python
message = "hello"
```

相关方法：

```python
首字母大写：str.title()
全部大写/小写：str.upper(),str.lower()
拼接字符 str1 + str2
转义符：\t 制表符   \n 换行
		str1+"\t"+str2
删除空白：头部lstrip(),尾部rstrip(),两端strip()
#python之禅：import this 自动输出python指导原则
```



#### 数字

```python
i = 1
f = 0.5
#运算：+ - * /
# ** 表示乘方
# % 取余/取模
str(i)#将数字转为字符串
#!!!! python只能拼接字符串，无法将数字和字符串直接拼接
```



#### 注释

单行注释：# balabalabala

多行注释：'''  balabalabala '''

#### 列表

```python
#可以将任何数据放入列表
ls = [1,"2"]
#操作列表
ls[0] #访问第一个
ls[-1]#访问最后一个
len(ls) #返回列表长度
ls[1] = 2 #修改
ls.append(3) #追加
ls.insert(1,"1") #插入到指定位置
#删除
del ls[0] #删除指定位置
i = ls.pop(0) #弹出指定元素（删除）
i = ls.pop() #弹出最后一个元素
ls.remove("2") #删除指定元素值（只删除一个最先出现的）
#排序(ls中的数据类型不同时会报错)
ls.sort() #永久改变ls的排序
ls.reverse() #永久反向排序
sorted(ls) #返回排好序的新ls，不影响原ls
sorted(ls,reverse=True) #反向排序,不能写true
0 in ls # 0是否在列表中
0 not in ls
```

#### 集合Set：不允许重复元素

```python
#list转set  去重
se = set(ls)
```



### 2. 操作列表

#### for循环

注意缩进和冒号

```python
for x in ls:
    print(x)
```

#### 创建数值列表：range()

```python
range(1,5) #生成 [1,5)的数 1,2,3,4
range(1,5,2) #生成[1,5)的数，步长为2 : 1,3,
print()
ls = list(range(1,5)) #生成数值列表[1,2,3,4]
#数字列表操作
max(ls)
min(ls)
sum(ls)
#生成数字列表
ls = [ x for x in range(1,10)]
```

#### 列表切片

```python
ls = [0,1,2,3]
ls1 = ls[0:2] #取[0,2)
ls2 = ls[:2] #从头取到索引2 即[0,2)
ls3 = ls[2:] #从索引3取到结束 即[3,len(ls) ）
ls4 = ls[-2:] #从倒数第二个取到结束 （倒数从-1开始）
ls5 = ls[:] #所有
for x in ls[:3]: #遍历切片
    print(x)
ls6 = ls[:] #数组复制
```



### 操作元组（不可变元素）

元组（dimensions）：不可变的列表，使用括号

```python
dim = (1,2,3) #定义元组
# 不可以直接修改元组数据 dim[0] = 5 报错
dim = (5)#通过重新赋值来改变元组
#其他操作与列表差不多
```



### if

#### 逻辑判断

```python
car == "bwm"
#组合判断
1 == 2 and 2 == 3
1 == 2 or 2 == 3
#元素是否在/不在列表、元组
0 in ls , 0 not in ls
# 布尔 首字母必须大写
True,False
# 判断列表是否为空
if ls:
    print("列表不为空")
```

#### if语句

```python
#简单if
if 1 > 2:
    print(xxx)
# if-else
if 1 > 2 :
    print(True)
else:
    print(False)
# if-elif-else
if 1 > 2:
    prinr(1>2)
elif 1 < 2:
    print(1<2)
else 1 == 2 :
    print(1==2)
```



### 字典

#### 创建字典

```python
dic = {"name":"Jissi","age":20}
print(dic["name"]) #输出字典中某个key的值
#字典可以嵌套字典,包含数组
dic1 = {"dic":dic，"ls":[1,2,3]}
#字典列表
ls = [dic,dic1]
```

#### 操作

```python
dic["age"] = 30 #添加或修改值
del dic["age"] #删除键值对
#遍历键值对
for key,value in dic.items()
#遍历所有key
for key in dic.keys()
#遍历所有value
for value in dic.values()
keys = list(dic.keys()) #将所有key转为lsit,同类values
#判断是否有key
if "name" in dic.keys()
#排序遍历
for key in sorted(dic.keys())
#列表转set去重
se = set(dic.values())
```



### 用户输入与while

#### 用户输入：input函数

```python
message = input("请输入")#等待用户输入 ,接收为字符串
num = int("2") #字符转int
```

### while循环

```python
message = input("input your word")
while( message != "exit"):
    print(message)
    message = input("input next word")
```



### 函数

#### 定义无参，带参数，默认参数函数

```python
#无参
def myFun(): print(123)
#带参
def myFun(name,age): print(name,age)
#带参，有默认值,可以不传实参,注意：没有默认值的形参要写在前面
def myFunDefault(name,age=22):print(name,age)
#任意参数数量：*X 将参数封装到X元组 ,注意：*X必须写在最后
def myFunList(name,age=22,*properties):print(properties)
#任意数量关键字实参(键值对参数), **X 将键值对参数封装到字典X
def myFunKeys(name,age=22,**dic):print(list(dic.keys()))
```



```python
#调用
myFun()
myFun("name",22) #必须遵循顺序
#关键字实参
myFun(age=22,name="name") #将实参与形参关联赋值，不必在乎顺序
#有默认参数,不传则使用默认
myfunDefault("name")
```



#### 返回值

```python
#姓，名，中间名 ，返回完整姓名
def myFun(firstName,LastName,middleName=''):
    return (firstName + middleName + LastName).title()
```

#### 将函数保存到模块中

```python
#在模块文件 MyModule.py文件中编写函数 myFun()
#在当前文件导入模块，即可使用模块中所有函数
import MyModule
MyModule.myFun()#调用函数
#从模块导入指定函数
from MyModule import myFun,myFun1,myFun2
myFun()#调用函数
#给函数指定别名
from MyModule import myFun as fun
fun()#调用函数
#给模块指定别名
import MyModule as my
my.myFun()#调用函数
```



### 类

```python
# 编写类
class Dog():
    def __init__(self,name,age):#构造方法，self是指向自身的引用，不用传参
        self.name=name
        self.age=age
    def sit(self):#self是指向自身的引用，不用传参
        print(self.name + "is now siting")
    def say(self,message):
        print(self.name+"--->"+message)
        
d = Dog('dog',20)# 创建实例
d.sit()#调用方法
d.say("labalablabla")

```

```python
# 继承
class Car():
    def __init__(self,make,year):
        self.make=make
        self.year=year
        self.mile = 0
     def car_info(self):
        print(self.name,self.year,self.mile)
class ElectricCar(Car):
    def __init__(self,make,year):
        super().__init__(make,year)#super()拿到父类的self
        self.battery = 100#新增子类属性
    def car_info(self):
        print(123)#重新父类方法
e = ElectricCar('tesla',2020)
e.car_info()
```

```python
# 导入类  类在my_car.py中
from my_car import Car,ElectricCar #从模块导入多个类
import my_car #导入整个模块
```



##### python标准库 collecions

* 有序字典

  ```python
  from collections import OrderedDict
  dic = OrderedDict()
  dic['name'] = 'abi'
  dic['age'] = 12
  dic['gender'] = 'female'
  print(dic)
  ```

  



### 文件和异常

```python
with open('test.txt') as file:
    content = file.read()
    print(content)
    #逐行读取
    for line in file:
        print(line)
```

