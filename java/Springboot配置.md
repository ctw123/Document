# Springboot配置

## 统一异常处理

> 使用`@RestControllerAdvice`注解

```java
@RestControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(Exception.class)
    public String handleException(Exception e) {
        if (e instanceof ArithmeticException) {
            return "data error";
        }
        if (e instanceof Exception) {
            return "service error";
        }
        retur null;
    }
}
```

