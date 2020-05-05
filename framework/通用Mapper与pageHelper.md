### Mybatis插件

[toc]

#### 通用mapper

* 简化开发，单表查询自动生成

1. 导入依赖

   ```xml
   tk.mybatis
   mapper-spring-boot-starter
   2.1.5
   ```

2. 配置文件

   ```yml
   mybatis:
   	configuration: 
		map-underscore-to
   ```
   
   



#### 分页插件PageHelper