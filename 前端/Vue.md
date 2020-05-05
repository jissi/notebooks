### vue

采用简洁的模板语法来声明式地将数据渲染进 DOM 的系统

* 简单使用

  在元素上显示数据

  ```html
  <div id="app1">{{user.name}}</div>
  <script>
      var user = {"name":"Aci"};
      new Vue({
          el: '#app1',
          data: {
              message: user
          }
      })
  </script>
  ```

  

##### 监听事件 v-on: <==> @

```html
<div id="app" v-on:click="count">点击次数：{{num}}</div>
<div id="app" @click="count">点击次数：{{num}}</div>
<script>
    new Vue({
        el: '#app',
        data:{
            num: 0
        },
        method: {
            count: function(){
                this.num++;
            }
        }
    })
</script>
```

* 事件修饰符

  ```html
  <div class="ui container" id="content">
      <div class="ui segment" id="clk1" @click="show">
          <div class="ui segment" id="clk2" @click="show">
              <div class="ui segment" id="clk3" @click="show"></div>
          </div>
      </div>
  </div>
  <script>
      new Vue({
          el: '#content',
          data:{},
          methods:{
              show:function () {
                  var id = event.currentTarget.id;
                  alert(id);
              }
          }
      })
  ```

  

  v-on:click.stop  //只触发当前元素的事件，不会传递到父级

  .capture //优先触发

  .self //只有本身被点击才触发，不接受子级传递

  @submit.prevent  //阻止页面刷新或跳转



##### 条件语句

* v-if  满足条件显示
* v-else    
* v-else-if  // v-else-if="show == 1"



##### 循环

将id绑定到父级上

自身参与循环

* v-for="user in users"

* v-for="user,index in users"   // index从0开始

* v-for="i in 10"   

  ```js
  // v-for 动态删除
  var ls = new Vue({
      el:'#table',
      data:{
          info:[1,2,3]
      }
  })
  ls.info.splice(0,1);//删除索引为1的元素，刷新数据
  ```

  

##### 属性绑定 ：将标签属性与数据绑定

* v-bind:href="page"
* v-bind:href 简写为 :href

##### 双向绑定：将标签(view)的值绑定到vue

* v-model="name"   //将标签的value 绑定到vue的name上

  默认一旦value发生变化，立即同步到vue

* 修饰符
  * v-model.lazy="name"  // 当该标签的值失去焦点后再同步到vue
  * v-model.number="age"  //将值转为数字类型，（默认的类型是string）
  * v-model.trim="name"  //去掉前后空格



##### 数据运算  : computed有缓存，数据没有变动的情况下直接从缓存取值

* computed

  ```html
  <div id="app"><div>{{doller}}</div></div>
  new Vue({
      el:'#app'
      data:{i:2,j:4},
  	computed:{
          doller:function(){return this.j/this.i}
      }
  })
  ```

  

* methods：在{{方法()}} 调用方法需要有()

  ```html
  <div id="app"><div>{{getDoller()}}</div></div>
  new Vue({
      el:'#app'
      data:{i:2,j:4},
  	methods:{
          getDoller:function(){return this.j/this.i}
      }
  })
  ```



##### watch 监听属性值变化

```html
<div id="app"><input v-model.number="i"/></div>
new Vue({
	el: '#app',
	data:{i:0},
	watch:{
	i:fuction(val){
		alert(val)
		}
	}
})

```



##### 过滤器：filters  有点像linux的管道

```html
<div id="app"><div v-model="i">{{i|add|sum}}</div></div>
new Vue({
	el:'#app',
	data:{i:0},
	filters{
		add:function(val){return ++val},
		sum:function(val){return val+=val}
	}
})
```

* 全局过滤器，普通的过滤器都是写在Vue对象里的，不能全局通用

  ```html
  Vue.filter('add',function(val){
  	return ++val;
  })
  ```





##### 组件 components

关键词：components,template

```vue
<div id="content">
    <c1></c1>直接使用组件
    <c1></c1>
</div>
new Vue({
        el: '#content',
        data: {
            i:0
        },
        components:{
            'c1':{
                template:'<div class="ui segment">hi</div>'
            }
        }
    })
```

* 全局组件 Vue.components('tag',{...})

  ```vue
  Vue.component('g1',
  	{template:'<div class="ui segment">全局组件</div>'}
  );
  ```

  

* 传参：props:['name']  ，使用标签时设置同名属性

  ```html
  <g1 name="参数"></g1>
  Vue.component('g1',
          {
              props:['name'],
              template:'<div class="ui segment">{{name}}</div>'
          }
      );
  ```

* 绑定参数

  ```html
  <div id="app">
      <c1 v-bind:name="na"></c1>
  </div>
  new Vue({
  	el:'#app',
  	data:{
  		na:'绑定到na'
  	},
  	components:{
  		'c1':{
  			props:['name'],
  			template: '<div>{{name}}</div>'
  		}
  	}
  })
  ```

* 绑定事件

  ```html
  Vue.component('g1',
          {
              props:['name'],
              template:'<div class="ui segment" @click="show">{{name}}</div>',
              methods:{
              show:function(){this.name++}
              }
          }
      );
  ```

* 遍历json数组

  ```html
  <div class="ui container" id="content">
      <jn v-for="user in users" v-bind:user="user"></jn>
  </div>
  <script>
      Vue.component('jn',{
          props:['user'],
          template:'<div class="ui segment">{{user.name}} --> {{user.id}}</div>'
      })
      new Vue({
          el:'#content',
          data:{
              users:[
                  {"id":"1","name":"Aci"},
                  {"id":"2","name":"Abi"}
              ]
          }
      })
  </script>
  ```

* 自定义指令

  ```html
  <div class="ui container" id="content">
      <div v-test="{'text':'hahah'}">Hi,</div>
  </div>
  Vue.directive('test',function(el,val){ //el参数是固定不要改，代表当前dom，后面是参数
  	el.innerHTML = el.innerHTML + val.value.text
  })
  new Vue({
  	el:'#content'
  })
  ```

* ajax动态刷新数据

  ```js
  var types = new Vue({
              el: '#typeInfo',
              data:{
                  tags: {}
              }
      });
  
  $.get("/goods/type/"+typeId,function (result) {
              types.tags = result.data;
          },"json");
  ```

  