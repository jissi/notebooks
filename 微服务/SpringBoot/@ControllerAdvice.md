

### @ControllAdvice使用的三种场景

1. 全局异常处理

   ```@ExceptionHandler(Exception.class)```指定方法负责处理的异常

```java
@ControllerAdvice
public class GlobalExcetionHandler {
    //指定要处理的异常
    @ExceptionHandler(Exception.class)
    public ModelAndView errorPage(HttpServletRequest request,Exception e) throws Exception{
        ModelAndView mav = new ModelAndView();
        mav.addObject("URL",request.getRequestURL());
        mav.addObject("Exception",e.getMessage());
        mav.setViewName("error");
        return mav;
    }
}
```

2. 全局数据绑定（定义全局数据,）

   ```@ModelAttribute( name = "globalData")```指定全局数的key

```java
@ControllerAdvice
public class GlobalExcetionHandler {
        @ModelAttribute(name = "globalData")
    public Map<String,Object> map(){
        Map<String,Object> map = new HashMap<>(2);
        map.put("uid",2);
        map.put("uname","globalData");
        return map;
    }
}

// 在Controller中可以取出全局参数
@Controler 
public .......(Model model){
    Map<String,Object> map = model.asMap();
    Map<String,Object> data = map.get("globalData");
    ...
}
```

3. 全局数据预处理（实现区分不同实体类的同名属性）

   ```@InitBinder(value = "u")```该方法处理带 u 前缀的参数，方法中声明前缀"u."，传递参数是也要加前缀

```java
@ControllerAdvice
public class GlobalExcetionHandler {
        @InitBinder(value = "u")
    public void u(WebDataBinder binder){
        binder.setFieldDefaultPrefix("u.");
    }
}

@Controller中声明参数前缀
public void custAndUser(@ModelAttribute("u") User user, @ModelAttribute("c") Customer customer){};

页面传递参数：
        <input type="text" name="u.uid">
        <input type="text" name="u.uname">
```

