# WebSocket-Java实现

## 导入依赖

```xml
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-websocket</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
```

## 初始化Bean,开启WebSocket功能

```java
@EnableWebSocket
@SpringBootApplication
public class WebSocketApplication {

    public static void main(String[] args) {
        SpringApplication.run(WebSocketApplication.class, args);
    }

    /**
     *  初始化Bean，它会自动注册使用了 @ServerEndpoint 注解声明的 WebSocket endpoint
     * @return
     */
    @Bean
    public ServerEndpointExporter serverEndpointExporter(){
        return new ServerEndpointExporter();
    }
}

```

> @EnableWebSocket 开启WebSocket功能

## 编写工具类

```java
import javax.websocket.RemoteEndpoint;
import javax.websocket.Session;
import java.io.IOException;
import java.util.Map;
import java.util.concurrent.ConcurrentHashMap;

public class WebSocketUtil {

    /**
     * 模拟存储   在线用户
     */
    public static final Map<String, Session> USERS_ONLINE = new ConcurrentHashMap<>();

    /**
     * 向所有在线用户发送消息(遍历 向每一个用户发送)
     * @param message
     */
    public static void sendMessageToAllOnlineUser(String message){
        USERS_ONLINE.forEach((username, Session) -> sendMessage(Session, message));
    }

    /**
     * 向指定用户发送消息
     * @param session 用户session
     * @param message 发送消息内容
     */
    private static void sendMessage(Session session, String message) {
        if (session == null) {
            return;
        }

        final RemoteEndpoint.Basic basic = session.getBasicRemote();
        if (basic == null) {
            return;
        }

        try {
            basic.sendText(message);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

}
```

## 编写端点

```java
import com.ctw.websocket.Utils.WebSocketUtil;
import lombok.extern.slf4j.Slf4j;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;

import javax.websocket.OnClose;
import javax.websocket.OnError;
import javax.websocket.OnMessage;
import javax.websocket.OnOpen;
import javax.websocket.Session;
import javax.websocket.server.PathParam;
import javax.websocket.server.ServerEndpoint;
import java.io.IOException;


@Slf4j
@Controller
@ServerEndpoint("/chat/{username}") //说明创建websocket的endpoint
public class WebSocketServer  {

    /**
     * 访问聊天室页面
     * @return
     */
    @GetMapping("/chatPage")
    public String chatPage(){
        return "chat.html";
    }


    //onopen 在连接创建(用户进入聊天室)时触发
    @OnOpen
    public void openSession(@PathParam("username") String username, Session session){
        //存储用户
        WebSocketUtil.USERS_ONLINE.put(username, session);
        //向所有在线用户发送用户上线通知消息
        String message = "["+username+"]进入聊天室";
        System.out.println(message);
        WebSocketUtil.sendMessageToAllOnlineUser(message);
    }
    //onclose 在连接断开(用户离开聊天室)时触发
    @OnClose
    public void closeSession(@PathParam("username") String username, Session session){
        //删除用户
        WebSocketUtil.USERS_ONLINE.remove(username);
        //向所有在线用户发送用户下线通知消息
        String message = "["+username+"]离开了聊天室";
        System.out.println(message);
        WebSocketUtil.sendMessageToAllOnlineUser(message);
        //下线后关闭session
        try {
            session.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    //　onmessage 在接收到消息时触发
    @OnMessage
    public void onMessage(@PathParam("username") String username, String message){
        //向聊天室中的人发送消息
        message = "["+username+"]：" + message;
        System.out.println(message);
        WebSocketUtil.sendMessageToAllOnlineUser(message);
    }

    //onerror 在连接发生异常时触发
    @OnError
    public void sessionError(Session session, Throwable throwable){
        try {
            session.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
        System.out.println("WebSocket连接发生异常，message:"+throwable.getMessage());
    }
}

```

> @ServerEndpoint("/chat/{username}") //说明创建websocket的endpoint
>
> @onopen 在连接创建(用户进入聊天室)时触发
>
> @onclose 在连接断开(用户离开聊天室)时触发
>
> @OnMessage 在接收到消息时触发
>
> @OnError 在连接发生异常时触发