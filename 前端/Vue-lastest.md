# Vue

### 1. 简介

渐进式JavaScript框架

完成数据和视图的绑定，双向绑定 MVVM

注意：使用Vue的过程中页面不用再引入Jquery框架

* JavaScript框架
* 简化Dom操作
* 响应式数据驱动：页面由数据生成

### 2. Vue基础

```html
<div id="app">
    {{message}}
</div>
<script>
    var app = new Vue({
      el: '#app',
      data:{
          message: 'hello'
      }
    })
</script>
```



#### 2.1 el 挂载点

* Vue的作用范围：el命中的元素内部及其后代元素
* 也支持其他选择器：建议使用id选择器，其他选择器可能命中多个元素
* 只支持双标签(不能是html、body)，推荐div，因为其他标签会有自己的样式

#### 2.2 data 数据对象

* 复杂数据

  ```shell
  data:{
  	message: 'hello',
  	shcool: {         
  		name: '11',
  		age: 12
  	}，
  	list: [1,2,3] 
  }
  #使用 {{ school }} 显示整个对象，{{ school.name }} 取指定属性
  #数组 使用 {{ list }} 显示所有数据，{{ list[1] }} 取指定数据
  ```



#### 2.3 Vue指令

##### 2.3.1 内容绑定、事件绑定

* v-text 设置标签的文本值：会替换所有原有的文本值，只能将数据解析成文本

  ```shell
  # data:{ msg: 'hi' }
  <h2 v-text="msg"></h2>
  <h2>{{ msg }}</h2>
  # 表达式支持
  <h2 v-text="msg + '123' "></h2>
  <h2>{{ msg + "123" }}</h2>
  ```

* v-html 设置标签的 innerHtml   可以解析html ，普通文本与v-text一样

  ```shell
  # data:{ subTag: '<h1>子元素</h1>' }
  <h3 v-html="subTag"></h3>
  ```

* v-on: /@事件   绑定事件

  

  ```shell
  <div id="app">
  	<input type="button" value="单击" v-on:click="act"/>
  	<input type="button" value="移入" @mouseenter="act"/>
  	<input type="button" value="双击" @dblclick="act"/>
  </div>
  var app = new Vue({
      el: '#app',
      data:{
          info:'!'
      }
      methods:{
          act:funtion(){ # this.数据  拿到数据
              this.info += "!" #vue中不用直接操作dom，只需要修改数据，dom会同步更新
          }
      }
  })
  # 自定义参数 & 事件修饰符 更多事件修饰符百度
  <button @click="act(1)"></button>
  <button @keyup.enter="act(1)"></button>#回车键时触发
  <button @click="dom($event)"></button>#传入dom事件
  methods:{
          act:funtion(id){ 
              alert(id)
          },
          dom:function(event){
              alert(event.currentTarget)
          }
      }
  ```

  

##### 2.3.2 显示切换、属性绑定

* v-show  根据表达式的真假 切换元素的显示与隐藏(修改display样式)

  ```shell
  # data:{ age:17,isShow:true}
  <h1 v-show="age>=18"></h1>
  <h1 v-show="isShow"></h1>
  ```

* v-if 根据表达式真假 切换元素的显示与隐藏（直接操作dom元素，频繁使用导致性能下降）

  ```shell
  <h1 v-if="age < 18">小于18</h1>
  <h1 v-else-if="age == 18">刚好</h1>
  <h1 v-else>大于18</h1>
  ```

* v-bind:属性名  可以简写 :属性名  设置元素属性（eg：src,title,class）

  ```shell
  # data:{ isActive:false,imgSrc:'xxxx'}
  <img v-bind:src="imgSrc"/>
  <button v-bind:class="isActive?'active':''"></button> #设置class样式,三元表达式写法
  <button :class="{active:isActive}"></button># 设置class样式，对象写法
  ```

  

##### 2.3.3 列表循环、表单元素绑定

* v-for 根据数据生成列表结构

  ```shell
  # data:{ list:[1,2,3] }
  <h1 v-for="item in list">{{ item }}</h1>
  <h1 v-for="(item,index) in list">{{ item }} {{ index }}<h1>
  ```

* v-model 获取和设置表单元素的值（双向数据绑定）

  ```shell
  # data:{ message:'msg'}
  # 将表单的value绑定到message
  <input type="text" v-model="message"/>
  # 修改message的值 表单会同步变化，修改表单 message同步变化
  ```


#### 2.4 computed 属性计算：封装复杂计算过程

```shell
<h1>{{ indata-outdata }}</h1># 不使用computed函数
<h1>{{ pure }}</h1> # 使用computed函数
var vue = new Vue({
    el:'#app',
    data:{
        indata:0,
        outdata:0
    },
    computed:{
        pure:function(){ # 计算属性的值并返回结果，如果属性没有发生变化，直接返回上次的结果(缓存值)，而methods 每次都要计算一遍
            return this.indata - this.outdata
        }
    }
})
```

#### 2.5 watch 监听器 监听表单值变化

```shell
<input type="text" v-model="num"/>
var vue = new Vue({
    el:'#app',
    data:{
        num:0
    },
    watch:{
        num:function(val){ #监听num属性变化事件
            console.log("num的值被修改为"+val)
        }
    }
})
```

#### 2.6 filtes 过滤器 过滤数据

```shell
<h1>{{ str | capitalize }}</h1> #数据经过过滤器处理，可以有多个过滤器
var vue = new Vue(){
    ....
    data:{
        str:'abc'
    },
    filters:{
        capitalize:function(value){ # 首字母转大写
       		return 
       			value.charAt(0).toUpperCase()
       				+value.substring(1)
        }
    }
}

# ---- 全局过滤器,可以在其他vue对象使用
Vue.filter('过滤器名',function(value){
    # 处理逻辑
    return xxx;
})
```

#### 2.7 components 组件

* 局部组件

  ```shell
  <div id="app">
  	<info></info> #使用组件
  </div>
  var vue = new Vue({
      ...
      components:{
          'info':{
              template:'<div>信息</div>'
          }
      }
  })
  ```

* 全局组件

  ```shell
  Vue.component('组件名/标签名',{
      template:'<div>信息</div>'
  })
  ```

* 组件参数

  ```shell
  Vue.component('info',{
      props:['name'], # 接收name参数,可以有多个参数
      template:'<div>{{ name }}</div>'
  })
  <info name="123"></info> ==> <div>123</div>
  
  # 使用动态参数：使用组件时 v-bind:参数="数据"
  # vue.. data:{tips:'123'}
  <info v-bind:name="tips"></info>
  ```

* 组件绑定事件

  ```shell
  Vue.component('info',{
      props:['name'],
      template:'<div @click="act">{{ name }}</div>',
      methods:{
          act:function(){
              aler()
          }
      }
  })
  ```

* 组件遍历数组

  ```shell
  # data:{ list:[1,2,3] }
  Vue.component('info',{
      props:['item'],
      template:'<div>{{ item }}</div>'
  })
  
  # 使用 for循环取出 值 再将值绑定到 组件参数上
  <info v-for="i in list" v-bind:item="i"></info>
  ```

#### 2.8 自定义指令

* Vue.directive定义指令

  ```shell
  # 无参指令
  Vue.directive('指令名',function(el){ # 定义指令，操作挂载的元素
  	el.innerHTML = el.innerHTML + 'test';
  	el.style.color='red'
  })
  # 使用 
  <div v-指令名></div>
  
  # 有参指令
  Vue.directive('指令名',function(el,binding){
  	el.innerHTML = binding.value
  })
  # 使用
  <div v-指令名="123"></div>
  ```

  

### 3. 路由 router

* Vue工程目录中只有一个index html文件，页面的跳转都是在该html上重新设置组件，该操作通过路由来实现，所以说vue的路由是局部刷新

* 导入依赖

  ```shell
  <script src="https://unpkg.com/vue-router/dist/vue-router.js"></script>
  
  # 路由跳转
  <router-link to="/mail"/>
  this.$router.push("/mail")
  this.$router.push({name:'/mail',query:{id:1}} ) # 类似get请求  取参数：this.$route.query.id
  this.$router.push( {path:'/mail'},query:{id:1} )
  this.$router.push( {name:'/mail'},params:{id:1} ) # 类似post
  this.$router.replace() # 用法与push类型
  this.$router.go( n ) #向前向后跳转
  ```

* 使用路由

  ```shell
  # -------------------------js部分
  # 定义组件，用来在页面上显示
  const user = { template:'<div>user</div>'};
  const cust = { template:'<div>cust</div>'};
  const emp = { template:'<div>emp</div>'};
  # 配置路由映射关系列表
  const routes = [
      {path:'/',redirect:'/user'}, # / 请求重定向到 /user
      # /user请求时将组件设置为user组件
      {path:'/user',component:user}, 
      {path:'/cust',component:cust},
      {path:'/emp',component:emp}
  ]
  # 创建路由器 , 将其 routers属性赋值为 上面定义的路由列表
  const router = new Vue({ routers:routers})
  # 将路由绑定到vue
  const vue = new Vue({
      el:'#app',
      router # 绑定路由实例
  })
  
  #-------------------页面部分
  <div id="app">
  	<div>
  		# router-link 类似 a标签；to 属性类似 href属性
  		<router-link to="/user">跳转user</touter-link>
  		.....
  	</div>
  	<div>
  		# 点击菜单时，刷新该部分
  		<router-view></router-view>
  	</div>
  </div>
  
  
  # 有多个路由时
  #---页面
  <route-view></route-view>
  <toute-view name='a'></route-view>
  # --- 路由列表
  const routes = [
      {path: '/user',components:{
          	default: user, #没有指定name实现的view使用user组件
          	a: emp # name为a的view使用emp组件
      	}
      }
  ]
  ```

  

### 4. 网络应用（axios + vue）

#### 4.1 axios 网络请求库

```html
<script src="https://unpkg.com/axios/dist/axios.min.js"></script>
```

* 请求

  <font color="red">注意：ajax方法中的this代表当前ajax对象，而不是vue的this，需要在进入ajax方法之前为vue的this赋值给其他变量</font>

  ```js
  // -----------------get请求
  var that = this; //将vue的this保存到其他变量
  axios.get(url)
      .then(
      function(response){//请求成功触发
          that.info = response.data
      },
      function(err){}//请求失败触发
  )
  // ----
  this.axios({
  	method: "get",
  	url:`${this.baseURL}/GetAll`,
  	headers: {Account: "Admin",Password:"123456"}
      data:{}
  })
  .then((response)=> {
  	console.log(response)
  })
  .catch((error)=> {
  	console.log(error);
  });
  // ----
  this.axios.get('http://bt2.xyz:8083/solr/adminQuery', 
                 {params: {"page": 1,"rows": 5}
  })
  .then((response)=> {
  	console.log(response)
  })
  .catch((error)=> {
   console.log(error);
  });
  // -----------------post请求
  axios.post(url,{k1:v1,k2:v2})
      .then(
      function(response){},
      function(err){}
  )
  // ------------------delete请求
  axios.delete('url',{data: {key1: value1}})
  // ------------------put请求
  axios.put('url',{key1: value1,key2: value2})
  // -----------------patch
  axios.patch('url',{key1: value1,key2: value2})
  ```

#### Vue项目结构

##### src目录下的文件

```shell
App.vue
main.js
touter/index.js # 定义路由
components/xxx.vue #组件
assets
```

* .vue文件

  ```vue
  <template>
  	<div id="名称">
          各种子标签
      </div>
  </template>
  <script>
      export default{
          data(){
              return{
                  info:'info'
              }
          },
          methods:{}
      }
  </script>
  ```





### 使用日志

* 引入semantic UI  <https://segmentfault.com/a/1190000007248072>

  1. 安装jquery： npm install --save jquery

  2. 安装 semanticUI ：npm install semantic-ui-css --save

  3. 修改  build/webpack.dev.config.js 文件（vue 3 ：项目根目录下创建 `vue.config.js`）

     ```js
     // plugins 区块内
     new webpack.ProvidePlugin({
       $              : "jquery",
       jQuery         : "jquery",
       "window.jQuery": "jquery",
       "root.jQuery"  : "jquery",
        // Semantic-UI
       semantic     : 'semantic-ui-css',
       Semantic     : 'semantic-ui-css',
       'semantic-ui': 'semantic-ui-css'
     })
     ```

  4. 修改 build/ webpack.base.config.js 文件

     ```js
     // resolve 区块
     resolve      : {
         extensions: ['', '.js', '.vue'],
         fallback  : [path.join(__dirname, '../node_modules')],
         alias     : {
           'vue'       : 'vue/dist/vue.common.js',
           'src'       : path.resolve(__dirname, '../src'),
           'assets'    : path.resolve(__dirname, '../src/assets'),
           'components': path.resolve(__dirname, '../src/components'),
           // Semantic-UI
           'semantic'  : path.resolve(__dirname, '../node_modules/semantic-ui-css/semantic.min.js')
         }
     }
     ```

  5. 在main.js引入

     ```js
     import semantic from 'semantic'
     import '../node_modules/semantic-ui-css/semantic.min.css'
     ```

  
  

