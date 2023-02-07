# Springboot整合Redis

## 报错示例

在使用lettuce作为redis连接池管理的时候,启动报错如下:

```text
Error creating bean with name ‘redisConnectionFactory‘ defined in class path resource
```

原因是因为只引入了starter-redis启动依赖.

解决:引入commons-pool2依赖即可:

```xml
        <dependency>
            <groupId>org.apache.commons</groupId>
            <artifactId>commons-pool2</artifactId>
        </dependency>
```

## Redis作为简单的消息队列

### 1.配置RedisTemplate模板

```java
@Configuration
public class RedisConfig {

    @Bean
    public RedisTemplate<String, Object> redisTemplate(LettuceConnectionFactory factory) {
        RedisTemplate<String, Object> redisTemplate = new RedisTemplate<>();
        redisTemplate.setConnectionFactory(factory);
        return redisTemplate;
    }

}
```

### 2.配置消息队列接收到消息后的工作

```java
@Component
public class RedisReceiver {

    public void handleMessage(String message){
        System.out.println(message);
    }

}
```

### 3.配置消息队列接收器

```java
@Component
public class RedisListenerInfo {

    @Bean
    RedisMessageListenerContainer container(RedisConnectionFactory connectionFactory, MessageListenerAdapter listenerAdapter){
        RedisMessageListenerContainer container = new RedisMessageListenerContainer();
        container.setConnectionFactory(connectionFactory);
        container.addMessageListener(listenerAdapter, PatternTopic.of("test"));
        return container;
    }

    @Bean
    MessageListenerAdapter listenerAdapter(RedisReceiver redisReceiver){
        return new MessageListenerAdapter(redisReceiver);
    }
}
```

