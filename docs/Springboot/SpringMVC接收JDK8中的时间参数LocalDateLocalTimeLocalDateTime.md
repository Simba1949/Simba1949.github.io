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
package top.simba1949.controller;

import lombok.extern.slf4j.Slf4j;
import org.springframework.web.bind.annotation.*;
import top.simba1949.common.User;

import java.time.LocalDate;
import java.time.LocalDateTime;
import java.time.format.DateTimeFormatter;

/**
 * @Author Theodore
 * @Date 2019/11/20 11:42
 */
@Slf4j
@RestController
@RequestMapping("user")
public class UserController {

    @PostMapping
    public User insert(@RequestBody User user){
        log.debug("insert user is {}", user);
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
        LocalDate parse = LocalDate.parse("2018-01-02", pattern);


        // JDK8 中月份从 1~12, 星期从 1~7
        int year = now.getYear();
        int month = now.getMonth().getValue();
        int dyaOfWeek = now.getDayOfWeek().getValue();

        return user;
    }
}
```

### Controller

```java
package top.simba1949.controller;

import lombok.extern.slf4j.Slf4j;
import org.springframework.web.bind.annotation.*;
import top.simba1949.common.User;

import java.time.LocalDate;
import java.time.LocalDateTime;
import java.time.LocalTime;
import java.time.format.DateTimeFormatter;
import java.time.temporal.TemporalAccessor;

/**
 * @Author Theodore
 * @Date 2019/11/20 11:42
 */
@Slf4j
@RestController
@RequestMapping("user")
public class UserController {

    @PostMapping
    public User insert(@RequestBody User user){
        log.debug("insert user is {}", user);
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
        LocalDate parse = LocalDate.parse("2018-01-02", pattern);


        // JDK8 中月份从 1~12, 星期从 1~7
        int year = now.getYear();
        int month = now.getMonth().getValue();
        int dyaOfWeek = now.getDayOfWeek().getValue();

        return user;
    }
}
```

### 配置文件

```yaml
server:
  port: 18080
logging:
  level:
    root: debug
```

