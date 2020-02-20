<h1>SpringBoot异常页面</h1>

[TOC]

---

##### SpringBoot处理异常时，所有异常条件都匹配才会跳转/error

```
异常处理流程：500错误->查找动态500.html->查找静态500.html->查找动态5xx.html->查找静态50xx.html->/error
```

#### 一、静态异常页面：resources/static/error/404.html、4xx.html

#### 二、动态异常页面：resources/templates/error/404.html、4xx.html

#### 三、自定义异常数据: extends DefaultErrorAttributes

* 默认的异常数据定义在 ``` DefaultErrorAttributes.getErrorAttributes```

  ```json
  timestamp、path、status、error、message
  ```

  DefaultErrorAttributes本身是在```ErrorMvcAutoConfiguration```中定义的，如果自己没有提供```ErrorAtttibutes```的实例，SpringBoot默认提供DefaultErrorAttributes

* 自定义异常数据的方式：实现ErrorAttributes 或者 继承DefaultErrorAttributes（推荐）

  DefaultErrorAttributes已经完成了对异常数据的处理，使用更方便

```java
@Component
public class CustErrorAttr extends DefaultErrorAttributes{
    @Override
    public Map<String,Object> getErrorAttributes(WebRequest webRequest,boolean includeStackTrace){
        Map<String,Object> map = super.getErrorAttributes(webRequest,includeStackTrace);
        if("500".equals(map.get("status"))){
            map.put("message","服务器内部异常");
        }
        return map;
    }
}
```



#### 四、自定义异常视图

BasicErrorController的errorHtml方法来返回异常ModelAndView、error方法用来返回ajax异常

在errorHtml方法构造ModelAndView对象时,如果构造成功则返回构造的modelAndView，如果失败则构造默认的并返回

```java
return (modelAndView != null) ? modelAndView : new ModelAndView("error", model);
```

调用DefaultErrorViewResolver的resolveErrorView方法来生成ModelAndView对象

而DefaultErrorViewResolver是在自己没有提供ErrorViewResolver实例时默认提供的，继承DefaultViewResolver即可自定义异常视图

```java
@Component
public ...... extends DefaultErrorViewResolver{
    public MyErrorViewResolver(ApplicationContext applicationContext, ResourceProperties resourceProperties) {
        super(applicationContext, resourceProperties);
    }
    @Override
    public ModelAndView resolveErrorView(HttpServletRequest request, HttpStatus status, Map<String, Object> model) {
        // 此时也可以自定义异常数据
        return new ModelAndView("/aaa/123", model);
    }
}
```

