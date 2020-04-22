# SpringMVC 接收 JDK8 中的时间参数（LocalDate/LocalTime/LocalDateTime）

## 前言

博客书

版本说明

```properties
JDK=1.8.0_221
springboot=2.2.0.RELEASE
```

相关链接

## 演练

### 实体类

```java
package top.simba1949.common;

import com.fasterxml.jackson.annotation.JsonFormat;
import lombok.Data;

import java.io.Serializable;
import java.time.LocalDate;
import java.time.LocalDateTime;
import java.time.LocalTime;

/**
 * @Author Theodore
 * @Date 2020/1/19 10:21
 */
@Data
public class User implements Serializable {

    private static final long serialVersionUID = -552127380120581998L;

    private Integer id;
    private String username;
    /**
     * LocalDateTime 本身带有时区信息
     * @JsonFormat(pattern = "yyyy-MM-dd HH:mm:ss")
     * <li>pattern : 用于接收参数的格式 </li>
     */
    @JsonFormat(pattern = "yyyy-MM-dd HH:mm:ss")
    private LocalDateTime dateTime;
    @JsonFormat(pattern = "yyyy-MM-dd")
    private LocalDate date;
    @JsonFormat(pattern = "HH:mm:ss")
    private LocalTime time;
}
```

### Controller

```java
package top.simba1949.controller;

import com.alibaba.fastjson.JSON;
import lombok.extern.slf4j.Slf4j;
import org.springframework.web.bind.annotation.*;
import top.simba1949.common.User;

import java.time.LocalDate;
import java.time.LocalDateTime;
import java.time.LocalTime;
import java.time.format.DateTimeFormatter;

/**
 * @Author Theodore
 * @Date 2020/1/19 10:23
 */
@Slf4j
@RestController
@RequestMapping("user")
public class UserController {

    @GetMapping("say")
    public String sayHello(){
        return "Hello Tester";
    }

    /**
     * <p>
     *     Method : POST
     *     RequestBody:
          {
      	    "username": "李白",
      	    "dateTime": "2020-01-19 01:02:03",
      	    "date": "2020-01-02",
      	    "time": "04:05:06"
          }
     * </p>
     * @param user
     * @return
     */
    @PostMapping
    public String insert(@RequestBody User user){
        String userStr = JSON.toJSONString(user);
        log.info("————————————————————————————————————");
        log.info(userStr);
        log.info("————————————————————————————————————");


        LocalDateTime now = LocalDateTime.now();
        // 时间格式化
        // 创建格式化/解析模板
        DateTimeFormatter dateTimeFormatter = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss");
        // 格式化
        String format = dateTimeFormatter.format(now);
        // 时间解析
        // 创建格式化/解析模板
        DateTimeFormatter pattern = DateTimeFormatter.ofPattern("yyyy-MM-dd");
        // 解析
        LocalDate parse = LocalDate.parse("2020-02-02", pattern);


        // JDK8 中月份从 1~12, 星期从 1~7
        int year = now.getYear();
        int month = now.getMonth().getValue();
        int dyaOfWeek = now.getDayOfWeek().getValue();


        return "SUCCESS";
    }

    /**
     * <p>
     *     Method : GET
     *     RequestParams: start=2019-12-31 00:01:02&end=2020-12-31 00:01:02
     * </p>
     * @param start
     * @param end
     * @return
     */
    @GetMapping("date-time")
    public String list(LocalDateTime start, LocalDateTime end){
        log.info("start : {}; end : {}", start, end);
        return "LocalDateTime";
    }

    /**
     * <p>
     *     Method : GET
     *     RequestParams: start=2019-12-31&end=2020-12-31
     * </p>
     * @param start
     * @param end
     * @return
     */
    @GetMapping("date")
    public String list(LocalDate start, LocalDate end){
        log.info("start : {}; end : {}", start, end);
        return "LocalDate";
    }

    /**
     * <p>
     *     Method : GET
     *     RequestParams: start=00:01:02&end=23:59:59
     * </p>
     * @param start
     * @param end
     * @return
     */
    @GetMapping("time")
    public String list(LocalTime start, LocalTime end){
        log.info("start : {}; end : {}", start, end);
        return "LocalTime";
    }
}
```

### LocalDateTimeConfig 配置类

```java
package top.simba1949.config;

import org.apache.commons.lang3.StringUtils;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.core.convert.converter.Converter;
import java.time.LocalDate;
import java.time.LocalDateTime;
import java.time.LocalTime;
import java.time.format.DateTimeFormatter;

/**
 * 该配置用于获取 RequestParam或者PathVariable 时的日期、时间参数
 *
 * @Author Theodore
 * @Date 2020/1/19 10:27
 */
@Configuration
public class LocalDateTimeConfig {

    @Bean
    public Converter<String, LocalDateTime> localDateTimeConverter() {
        // 使用 lambda 表达式有问题，暂未解决
        return new Converter<String, LocalDateTime>() {
            @Override
            public LocalDateTime convert(String s) {
                if (StringUtils.isBlank(s)){
                    return null;
                }
                // 时间格式化
                // 创建格式化/解析模板
                DateTimeFormatter dateTimeFormatter = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss");
                // 解析
                LocalDateTime parse = LocalDateTime.parse(s, dateTimeFormatter);
                return parse;
            }
        };
    }

    @Bean
    public Converter<String, LocalDate> localDateConverter() {
        return new Converter<String, LocalDate>() {
            @Override
            public LocalDate convert(String s) {
                if (StringUtils.isBlank(s)){
                    return null;
                }
                // 时间格式化
                // 创建格式化/解析模板
                DateTimeFormatter dateTimeFormatter = DateTimeFormatter.ofPattern("yyyy-MM-dd");
                // 解析
                LocalDate parse = LocalDate.parse(s, dateTimeFormatter);
                return parse;
            }
        };
    }

    @Bean
    public Converter<String, LocalTime> localTimeConverter() {
        return new Converter<String, LocalTime>(){
            @Override
            public LocalTime convert(String s) {
                if (StringUtils.isBlank(s)){
                    return null;
                }
                // 时间格式化
                // 创建格式化/解析模板
                DateTimeFormatter dateTimeFormatter = DateTimeFormatter.ofPattern("HH:mm:ss");
                // 解析
                LocalTime parse = LocalTime.parse(s, dateTimeFormatter);
                return parse;
            }
        };
    }
}
```

### 启动类 Application

```java
package top.simba1949;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

/**
 * @Author Theodore
 * @Date 2020/1/19 10:20
 */
@SpringBootApplication
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
```

### 配置文件

```yaml
server:
  port: 8081
spring:
  application:
    name: local-date-time-learn
```

