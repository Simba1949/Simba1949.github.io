# Springboot集成WebSocket

## 前言

博客书

版本说明

```properties
spring-boot-starter-parent=2.2.4.RELEASE
```

相关链接：

* spring-boot-starter-websocket : https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-starter-websocket

参考链接：

* https://www.jianshu.com/p/8c4983a3ca2e

## 实战演练

### 核心依赖

```xml
<!-- https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-starter-websocket -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-websocket</artifactId>
</dependency>
```

### pom 依赖

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>top.simba1949</groupId>
    <artifactId>web-socket-springboot</artifactId>
    <version>1.0-SNAPSHOT</version>

    <properties>
        <finalName.text>web-socket-springboot</finalName.text>
        <!--https://mvnrepository.com/artifact/org.springframework.cloud/spring-cloud-dependencies -->
        <spring-cloud-dependencies.version>Hoxton.RELEASE</spring-cloud-dependencies.version>
        <!--https://mvnrepository.com/artifact/com.alibaba/fastjson -->
        <fastjson.version>1.2.67</fastjson.version>
        <!--https://mvnrepository.com/artifact/com.google.guava/guava -->
        <guava.version>28.2-jre</guava.version>
        <!--https://mvnrepository.com/artifact/cn.hutool/hutool-all -->
        <hutool-all.version>5.2.4</hutool-all.version>
    </properties>

    <!--spring-boot 和 spring-cloud 版本兼容参考 https://spring.io/projects/spring-cloud -->
    <!-- Inherit defaults from Spring Boot -->
    <!--https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-starter-parent -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.2.4.RELEASE</version>
    </parent>

    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>${spring-cloud-dependencies.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>

    <dependencies>
        <!--Core starter, including auto-configuration support, logging and YAML-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter</artifactId>
        </dependency>
        <!-- spring-cloud-context，使 bootstrap.properties 配置文件生效 -->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-context</artifactId>
        </dependency>
        <!--Starter for using Spring Boot's Actuator which provides production ready features to help you monitor and manage your application-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
        <!--Starter for testing Spring Boot applications with libraries including JUnit, Hamcrest and Mockito-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
        <!--Spring Boot Configuration Processor-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-configuration-processor</artifactId>
        </dependency>
        <!--Spring Boot Developer Tools-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <optional>true</optional> <!-- 表示依赖不会传递 -->
        </dependency>

        <!--Common tools starter-->
        <!--lombok-->
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
        </dependency>
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>fastjson</artifactId>
            <version>${fastjson.version}</version>
        </dependency>
        <dependency>
            <groupId>com.google.guava</groupId>
            <artifactId>guava</artifactId>
            <version>${guava.version}</version>
        </dependency>
        <dependency>
            <groupId>cn.hutool</groupId>
            <artifactId>hutool-all</artifactId>
            <version>${hutool-all.version}</version>
        </dependency>
        <!--Common tools end -->

        <!-- https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-starter-websocket -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-websocket</artifactId>
        </dependency>
    </dependencies>

    <build>
        <finalName>${finalName.text}</finalName>
        <plugins>
            <!--The Compiler Plugin is used to compile the sources of your project.-->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <configuration>
                    <source>1.8</source>
                    <target>1.8</target>
                </configuration>
            </plugin>
            <!--The Resources Plugin handles the copying of project resources to the output directory. -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-resources-plugin</artifactId>
                <configuration>
                    <encoding>UTF-8</encoding>
                </configuration>
            </plugin>
            <!--Spring Boot Maven Plugin-->
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <configuration>
                    <!--main of springboot project-->
                    <!--<mainClass>top.simba1949.Application</mainClass>-->
                    <!-- 如果没有该配置，devtools不会生效 -->
                    <fork>true</fork>
                    <skip>true</skip>
                    <!--将项目注册到linux服务上，可以通过命令开启、关闭以及伴随开机启动等功能-->
                    <executable>true</executable>
                </configuration>
                <executions>
                    <execution>
                        <goals>
                            <goal>repackage</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>
            <!--Maven Surefire MOJO in maven-surefire-plugin.-->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-surefire-plugin</artifactId>
                <configuration>
                    <!--跳过测试-->
                    <skipTests>true</skipTests>
                </configuration>
            </plugin>
            <!-- docker的maven插件，详情请见 https://blog.csdn.net/SIMBA1949/article/details/83064083-->
        </plugins>

        <!--IDEA是不会编译src的java目录的文件，如果需要读取，则需要手动指定哪些配置文件需要读取-->
        <resources>
            <resource>
                <directory>src/main/java</directory>
                <includes>
                    <include>**/*.xml</include>
                </includes>
            </resource>
            <resource>
                <directory>src/main/resources</directory>
                <includes>
                    <include>**/*</include>
                </includes>
            </resource>
        </resources>
    </build>
</project>
```

### 核心配置(WebSocketConfig)

```java
package top.simba1949.config;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.socket.server.standard.ServerEndpointExporter;

/**
 * @Author Theodore
 * @Date 2020/3/30 17:12
 */
@Configuration
public class WebSocketConfig {

    @Bean
    public ServerEndpointExporter serverEndpointExporter() {
        return new ServerEndpointExporter();
    }
}
```

### 核心代码(WebSocketServer)

```java
package top.simba1949.websocket;

import com.alibaba.fastjson.JSON;
import lombok.extern.slf4j.Slf4j;
import org.springframework.stereotype.Component;
import top.simba1949.modle.Message;

import javax.websocket.*;
import javax.websocket.server.PathParam;
import javax.websocket.server.ServerEndpoint;
import java.io.IOException;
import java.util.concurrent.ConcurrentHashMap;
import java.util.concurrent.atomic.AtomicInteger;

/**
 * 使用 userId 标识用户唯一
 * @Author Theodore
 * @Date 2020/3/30 17:13
 */
@Slf4j
@ServerEndpoint("/websocket/{userId}")
@Component
public class WebSocketServer {
    /** 静态变量，用来记录当前在线连接数。应该把它设计成线程安全的。*/
    private static final AtomicInteger ON_LINE_COUNT = new AtomicInteger(0);
    /**
     * concurrent包的线程安全 Set，用来存放每个客户端对应的 MyWebSocket 对象。
     * 若要实现服务端与单一客户端通信的话，可以使用Map来存放，其中 Key 可以为用户标识
     */
    private static ConcurrentHashMap<String, WebSocketServer> webSocketSet = new ConcurrentHashMap<>();
    /** 某个客户端的连接会话，需要通过它来给客户端发送数据 */
    private Session session;
    /** 当前发消息的人员 userId */
    private String userId;

    /**
     * 连接建立成功调用的方法
     * @param param
     * @param session
     * @param config
     */
    @OnOpen
    public void onOpen( @PathParam(value = "userId") String param, Session session, EndpointConfig config) {
        userId = param;
        this.session = session;
        // 加入map中
        webSocketSet.put(param, this);
        // 在线数加1
        int cnt = ON_LINE_COUNT.incrementAndGet();
        log.info("有连接加入，当前连接数为：{}", cnt);
        sendMessage(this.session, "连接成功");
    }

    /**
     * 连接关闭调用的方法
     */
    @OnClose
    public void onClose() {
        if (!userId.equals("")){
            // 从set中删除
            webSocketSet.remove(userId);
            int cnt = ON_LINE_COUNT.decrementAndGet();
            log.info("有连接关闭，当前连接数为：{}", cnt);
        }
    }

    /**
     * 收到客户端消息后调用的方法
     * @param message
     * @param session
     */
    @OnMessage
    public void onMessage(String message, Session session) {
        log.info("来自客户端的消息：{}", message);
        Message msg = JSON.parseObject(message, Message.class);
        if (msg.getType() == 0){
            // 群发
            sendToGroup(msg.getMessage());
        }else {
            // 私发
            sendToUser(msg.getTo(), msg.getMessage());
        }
    }

    /**
     * 发生错误时调用的方法
     * @param session
     * @param error
     */
    @OnError
    public void onError(Session session, Throwable error) {
        log.error("发生错误：{}，Session ID： {}",error.getMessage(),session.getId());
        error.printStackTrace();
    }

    /**
     * 发送消息，实践表明，每次浏览器刷新，session会发生变化。
     * @param session
     * @param message
     */
    public void sendMessage(Session session, String message) {
        try {
            session.getBasicRemote().sendText(String.format("%s (From Server，Session ID=%s)", message, session.getId()));
        } catch (IOException e) {
            log.error("发送消息出错：{}", e.getMessage());
            e.printStackTrace();
        }
    }

    /**
     * 群发消息
     * @param message
     */
    public void sendToGroup(String message) {
        for (String key : webSocketSet.keySet()) {
            Session session = webSocketSet.get(key).session;
            if(session != null && session.isOpen() && !userId.equals(key)){
                sendMessage(session, message);
            }
        }
    }

    /**
     * 指定 Session 发送消息
     * @param userId
     * @param message
     */
    public void sendToUser(String userId, String message) {
        WebSocketServer webSocketServer = webSocketSet.get(userId);
        if ( webSocketServer != null && webSocketServer.session.isOpen()){
            sendMessage(webSocketServer.session, message);
        }else{
            log.warn("当前用户不在线：{}",userId);
        }
    }
}
```

### Message 信息

```java
package top.simba1949.modle;

import lombok.Data;
import java.io.Serializable;

/**
 * @Author Theodore
 * @Date 2020/3/30 17:46
 */
@Data
public class Message implements Serializable {
    private static final long serialVersionUID = -6513719561109260800L;

    private Integer type;
    private String to;
    private String from;
    private String message;
}
```

### 启动类

```java
package top.simba1949;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

/**
 * @Author Theodore
 * @Date 2020/3/30 17:00
 */
@SpringBootApplication
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
```

### 前端页面

如果不支持可以引入：`stomp.js` 、`sockjs.js` 

```html
<!DOCTYPE html>
<html>
    <head>
        <meta charset="UTF-8">
        <title>WebSocket Demo</title>
    </head>
    <body>
        <h2>WebSocket Demo---- user0 </h2>
        <input id="text" type="text" /> 
        <button onclick="send()"> Send </button>   
        <button onclick="closeWebSocket()"> Close </button>
        <div id="message"></div>
    </body>
    
    <script type="text/javascript">
    // 判断当前浏览器是否支持WebSocket
    if('WebSocket' in window){
        websocket = new WebSocket("ws://localhost:8080/websocket/user0");
        console.log("link success")
    }else {
        alert('Not support websocket')
    }

    // 连接发生错误的回调方法
    websocket.onerror = function(){
        setMessageInnerHTML("error");
    };
    
    // 连接成功建立的回调方法
    websocket.onopen = function(event){
        setMessageInnerHTML("open");
    }
    
    // 接收到消息的回调方法
    websocket.onmessage = function(event){
        setMessageInnerHTML(event.data);
    }
    
    // 连接关闭的回调方法
    websocket.onclose = function(){
        setMessageInnerHTML("close");
    }
    
    // 监听窗口关闭事件，当窗口关闭时，主动去关闭websocket连接，防止连接还没断开就关闭窗口，server端会抛异常。
    window.onbeforeunload = function(){
        websocket.close();
    }
    
    // 将消息显示在网页上
    function setMessageInnerHTML(innerHTML){
        document.getElementById('message').innerHTML += innerHTML + '<br/>';
    }
    
    // 关闭连接
    function closeWebSocket(){
        websocket.close();
    }
    
    // 发送消息
    function send(){
        let message = document.getElementById('text').value;
        let data = {};
        // 群发 type = 0；私发 type = 1；
        data.type = 1;
        // 需要复制该页面 websocket = new WebSocket("ws://localhost:8080/websocket/user1"); 在创建一个user1用户
        data.to = 'user1';
        data.message = message;
        
        websocket.send(JSON.stringify(data));
    }
    </script>
</html>
```

