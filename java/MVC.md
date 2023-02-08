# MVC配置

## 拦截器注册

### 编写拦截器

```java
@Slf4j
@Component
public class TokenInterceptor extends HandlerInterceptorAdapter {

    @Resource
    private JwtUtils jwtUtils;

    public static ThreadLocal<String> threadLocal = new ThreadLocal<>();

    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        String token = request.getHeader("Authorization");
        //    判断token是否为空
        if (StringUtils.isEmpty(token)) {
            log.info("没有请求头");
            return false;
        }
        try {
            String decode = jwtUtils.decode(token);
            threadLocal.set(decode);
        } catch (Exception e) {
            Result<Object> result = Result.failedToMsg(e.getMessage());
            response.getWriter().write(JSON.toJSONString(result));
            return false;
        }
        return true;
    }
}

```

### 注册拦截器

```java

@Configuration
public class WebConfig implements WebMvcConfigurer {

    @Resource
    private TokenInterceptor tokenInterceptor;


    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(tokenInterceptor).addPathPatterns("/ua/**");
    }
}

```

> 使用` WebMvcConfigurationSupport` 会导致Jackson统一格式化返回失效



## 配置过滤器

```java
@Configuration
public class WebMvcConfig {

    @Bean
    public CorsWebFilter corsWebFilter() {

        UrlBasedCorsConfigurationSource source = new UrlBasedCorsConfigurationSource();

        CorsConfiguration corsConfiguration = new CorsConfiguration();
        //    配置跨域
        corsConfiguration.addAllowedHeader("*");
        corsConfiguration.addAllowedOrigin("*");
        corsConfiguration.addAllowedMethod("*");
        corsConfiguration.setAllowCredentials(true);
        source.registerCorsConfiguration("/**",corsConfiguration);
        return new CorsWebFilter(source);
    }

}
```

> 过滤器不需要注册



## 拦截器和过滤器的区别

<img src="https://fastly.jsdelivr.net/gh/ctw123/my-img@master/1675823315467v2-bb74e1c3fbbb6fc39036e8003eb34a89_r.jpg" style="zoom:67%;" />

> 过滤器属于servlet的范围,拦截器属于springboot的组件，两者都体现了AOP的思想
>
> **过滤器的优先级高于拦截器**