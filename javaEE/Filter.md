# 过滤器

javax.servlet.Filter

---

### 1.接口方法

```java
default void init(FilterConfig filterConfig)
void doFilter(ServletRequest var1, ServletResponse var2, FilterChain var3) 
default void destroy() {}
```

java8以后支持默认方法：init和destroy是默认方法，可以不实现

### 2. 实现编码转换过滤器

```java
@WebFilter(urlPatterns = {"/*"})
public class EncodingFilter implements Filter{
    @Override
    void doFilter(ServletRequest req, ServletResponse res, FilterChain chain){
        req.setCharacterEncoding("UTF-8");
        // 要将过滤器链往下执行，否则请求在此中断
        chain.doFilter(req,res);
    }
}
```

### 3.声明周期

* tomcat启动时通过反射机制调用无参构造方法创建实例
* 调用init方法初始化
* doFilter发送请求时被调用
* tomcat关闭时调用destroy销毁

