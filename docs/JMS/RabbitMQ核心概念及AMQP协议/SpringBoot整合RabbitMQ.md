# SpringBoot整合RabbitMQ

## 前言

版本说明

```properties
rabbitmq=3.8.4
```

## 生产者

### pom

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>org.example</groupId>
    <artifactId>rabbitmq-producer</artifactId>
    <version>1.0-SNAPSHOT</version>

    <properties>
        <!--spring-boot 和 spring-cloud 版本兼容参考 https://spring.io/projects/spring-cloud -->
        <!-- https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-dependencies -->
        <spring-boot-dependencies.version>2.2.8.RELEASE</spring-boot-dependencies.version>
        <!--https://mvnrepository.com/artifact/org.springframework.cloud/spring-cloud-dependencies -->
        <spring-cloud-dependencies.version>Hoxton.RELEASE</spring-cloud-dependencies.version>

        <!-- the project final name -->
        <project.final.name>spring-boot-learn</project.final.name>

        <!--https://mvnrepository.com/artifact/com.alibaba/fastjson -->
        <fastjson.version>1.2.71</fastjson.version>
        <!--https://mvnrepository.com/artifact/com.google.guava/guava -->
        <guava.version>29.0-jre</guava.version>
        <!--https://mvnrepository.com/artifact/cn.hutool/hutool-all -->
        <hutool-all.version>5.3.7</hutool-all.version>
    </properties>

    <dependencyManagement>
        <dependencies>
            <dependency>
                <!-- Import dependency management from Spring Boot -->
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-dependencies</artifactId>
                <version>${spring-boot-dependencies.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>

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
            <scope>runtime</scope>
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

        <!-- https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-starter-amqp -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-amqp</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

    </dependencies>

    <build>
        <finalName>${project.final.name}</finalName>
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

### RabbitMQ核心配置类

```java
package top.simba1949.config;

import org.springframework.amqp.core.*;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

/**
 * RabbitMQ核心配置类
 * @author Theodore
 * @date 2020/6/17 15:08
 */
@Configuration
public class RabbitMQConfig {
    // 交换机名
    public static final String ITEM_TOPIC_EXCHANGE = "item_topic_exchange";
    // 队列名
    public static final String ITEM_QUEUE = "item_queue";
    // routing key
    public static final String ITEM_ROUTING_KEY = "item.#";

    /**
     * 声明交换机
     * @return
     */
    @Bean("itemTopicExchange")
    public Exchange topicExchange(){
        return ExchangeBuilder.topicExchange(ITEM_TOPIC_EXCHANGE).durable(true).build();
    }

    /**
     * 声明队列
     * @return
     */
    @Bean("itemQueue")
    public Queue itemQueue(){
        return QueueBuilder.durable(ITEM_QUEUE).build();
    }

    /**
     * 绑定队列和交换机
     * @param queue
     * @param exchange
     * @return
     */
    @Bean
    public Binding itemQueueExchange(@Qualifier("itemQueue") Queue queue,
                                     @Qualifier("itemTopicExchange") Exchange exchange){
        return BindingBuilder.bind(queue).to(exchange).with(ITEM_ROUTING_KEY).noargs();
    }
}
```

### 发送消息触发类

```java
package top.simba1949.controller;

import org.springframework.amqp.rabbit.core.RabbitTemplate;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;
import top.simba1949.config.RabbitMQConfig;

/**
 * @author Theodore
 * @date 2020/6/17 15:16
 */
@RestController
@RequestMapping("send")
public class SendController {
    /**
     * 注入 RabbitTemplate 模板
     */
    @Autowired
    private RabbitTemplate rabbitTemplate;

    @GetMapping
    public String sendMsg(String msg, String routingKey){
        /**
         * 发送消息
         * 参数一：交换机名称
         * 参数二：routingKey
         * 参数三：消息
         */
        rabbitTemplate.convertAndSend(RabbitMQConfig.ITEM_TOPIC_EXCHANGE, routingKey, msg);
        return "SUCCESS";
    }
}
```

### SpringBoot 启动类

```java
package top.simba1949;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

/**
 * @author Theodore
 * @date 2020/6/16 19:08
 */
@SpringBootApplication
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
```

### SpringBoot 配置

```yaml
server:
  port: 9000
spring:
  application:
    name: rabbitmq-producer
  rabbitmq:
    host: 192.168.8.9
    port: 5672
    username: guest
    password: guest
    virtual-host: /
```

## 消费者

### pom

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>org.example</groupId>
    <artifactId>rabbitmq-consumer</artifactId>
    <version>1.0-SNAPSHOT</version>

    <properties>
        <!--spring-boot 和 spring-cloud 版本兼容参考 https://spring.io/projects/spring-cloud -->
        <!-- https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-dependencies -->
        <spring-boot-dependencies.version>2.2.8.RELEASE</spring-boot-dependencies.version>
        <!--https://mvnrepository.com/artifact/org.springframework.cloud/spring-cloud-dependencies -->
        <spring-cloud-dependencies.version>Hoxton.RELEASE</spring-cloud-dependencies.version>

        <!-- the project final name -->
        <project.final.name>spring-boot-learn</project.final.name>

        <!--https://mvnrepository.com/artifact/com.alibaba/fastjson -->
        <fastjson.version>1.2.71</fastjson.version>
        <!--https://mvnrepository.com/artifact/com.google.guava/guava -->
        <guava.version>29.0-jre</guava.version>
        <!--https://mvnrepository.com/artifact/cn.hutool/hutool-all -->
        <hutool-all.version>5.3.7</hutool-all.version>
    </properties>

    <dependencyManagement>
        <dependencies>
            <dependency>
                <!-- Import dependency management from Spring Boot -->
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-dependencies</artifactId>
                <version>${spring-boot-dependencies.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>

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
            <scope>runtime</scope>
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

        <!-- https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-starter-amqp -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-amqp</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

    </dependencies>

    <build>
        <finalName>${project.final.name}</finalName>
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

### 消费者监听消息类

```java
package top.simba1949.consumer;

import org.springframework.amqp.rabbit.annotation.RabbitListener;
import org.springframework.stereotype.Component;

/**
 * 消费者监听类
 * @author Theodore
 * @date 2020/6/17 15:30
 */
@Component
public class MyRabbitMQListener {

    @RabbitListener(queues = {"item_queue"})
    public void msg(String msg){
        System.out.println("消费者接收到的消息：" + msg);
    }
}
```

### SpringBoot 启动类

```java
package top.simba1949;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

/**
 * @author Theodore
 * @date 2020/6/16 19:07
 */
@SpringBootApplication
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
```

### SpringBoot 配置

```yaml
server:
  port: 8000
spring:
  application:
    name: rabbitmq-consumer
  rabbitmq:
    host: 192.168.8.9
    port: 5672
    username: guest
    password: guest
    virtual-host: /
```
