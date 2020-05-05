# Vue

### 1. 引言

渐进式JavaScript框架

完成数据和视图的绑定，双向绑定 MVVM

注意：使用Vue的过程中页面不用再引入Jquery框架



### 2. 基本使用

```html
<div id="app"></div>
<!--vue引入省略-->
<script>
    var app = new Vue({
        el:"#app", //推荐使用id选择器
        data:{}
    })
</script>
```

