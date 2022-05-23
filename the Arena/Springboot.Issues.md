# Springboot Issues

## HandlerInterceptorAdapter::preHandle() 如何处理 exceptions ?

- 正常情况下 Interceptor 的 preHandle() 需要返回 true｜false；
  + true 表示继续进行处理， false表示放弃继续处理，直接返回 response；
- 但是在预处理过程中，依然可能会报错，同时抛出 exception，这时候不通的情况会有不通的处理；
  + 如果该请求的URL 对应有Controller，那么这个exception 就会被定义好的 ExceptionHandler 接收并直接处理返回；
  + 如果该请求的 URL 非法，并没有对应的 Controller，这时候 ServletDispatcher 会尝试请求 "/error"，进行处理；
    * 这时候保留实现一个对 "/error" 的 Controller，就可以处理好他们；
    
### Interceptor & Filter

Interceptor 和 Filter 都是可以在业务流程中，在正常业务流程之前，进行预先处理请求的办法。

Filter 一般是先于 Interceptor 进行处理的。

其中，Filter::doFilter() 可以环绕式来进行预处理，然后通过 filterChain 来控制流程继续处理; Interceptor最典型的使用就是 HandlerInterceptorAdapter, 通过继承这个类，也可以实现预处理；

使用 Interceptor 的另一个优势是，可以利用 set/get attribute() 来传递一些变量；

>需要注意的是，HandlerInterceptor::postHandle() 和 afterCompletion() 中的 HttpServletRequest / HTTPServletResponse 都是 read-only 只读的，无法进行修改，比如 添加或者修改 header -- 在interceptor中是做不到的，这时候可以使用 filter来处理；

>Filter 实现后需要做一个 FilterRegistrationBean 来注入Spring 中进行使用, Interceptor 实现之后也需要通过注册到 InterceptorRegistry 来进行使用。

> Interceptor 拦截器最好搭配 PathPatterns 来使用，避免对所有的 URL 进行拦截而出现意外情况，比如：
``` java
@Component
public class WebConfig implements WebMvcConfigurer {

    @Autowired
    private RequestInterceptor requestInterceptor;

    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(requestInterceptor)
                .addPathPatterns("/**")
                .excludePathPatterns("/user/login", "/versionInfo", "/error");
    }

}
```