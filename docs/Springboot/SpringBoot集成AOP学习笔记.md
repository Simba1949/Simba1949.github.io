# SpringBoot集成AOP学习笔记

## 前言

博客书

版本说明

```properties
spring.boot=2.2.3.RELEASE
```

相关链接

## 总的说明

```
无异常执行顺序：around before ——> before ——> 拦截的方法 ——> around after ——> after  ——> afterReturning

有异常执行顺序：around before ——> before ——> 拦截的方法  ——> after  ——> afterThrowing
```

* around before ：环绕通知前
* around after ：环绕通知后
* before ： 前置通知
* after ： 后置通知
* afterReturning ：返回后通知
* afterThrowing ：异常通知

## 实战演练

### pom.xml

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-aop</artifactId>
</dependency>
```

### Controller

```java
package top.simba1949.controller;

import lombok.extern.slf4j.Slf4j;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;

/**
 * @Author Theodore
 * @Date 2020/1/19 16:25
 */
@Slf4j
@RestController
@RequestMapping("user")
public class UserController {

    @GetMapping
    public String sayHello(@RequestParam Integer uid){
        log.info("uid is {}", uid);
//        int i = 1/0;
        return "Hello";
    }
}
```

### 切面

```java
package top.simba1949.aop;

import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.annotation.*;
import org.springframework.stereotype.Component;

/**
 * 定义切面
 * 无异常执行顺序：around before ——> before ——> 拦截的方法 ——> around after ——> after  ——> afterReturning
 * 有异常执行顺序：around before ——> before ——> 拦截的方法  ——> after  ——> afterThrowing
 * @Author Theodore
 * @Date 2020/1/19 17:41
 */
@Aspect
@Component
public class RequestAspect {

    /**
     * 定义切入点，切入点为com.example.demo.aop.AopController中的所有函数
     * 通过 @Pointcut 注解声明频繁使用的切点表达式
     */
    @Pointcut(value = "execution(public * top.simba1949.controller.*.*(..))")
    public void aspectCenter(){

    }

    /**
     * 在连接点执行之前执行的通知
     */
    @Before(value = "aspectCenter()")
    public void before(){
        System.err.println("before");
    }

    /**
     * 在连接点执行之前执行的通知
     */
    @After(value = "aspectCenter()")
    public void after(){
        System.err.println("after");
    }

    /**
     * 在连接点执行之后执行的通知（返回通知）
     */
    @AfterReturning("aspectCenter()")
    public void afterReturning(){
        System.err.println("afterReturning");
    }

    /**
     * 在连接点执行之后执行的通知（异常通知）
     */
    @AfterThrowing("aspectCenter()")
    public void afterThrowing(){
        System.err.println("afterThrowing");
    }

    /**
     * 环绕通知接受 ProceedingJoinPoint 作为参数，它来调用被通知的方法。
     * 通知方法中可以做任何的事情，当要将控制权交给被通知的方法时，需要调用ProceedingJoinPoint的proceed()方法。
     * 当你不调用proceed()方法时，将会阻塞被通知方法的访问。
     * @param pjp
     */
    @Around("aspectCenter()")
    public void around(ProceedingJoinPoint pjp) throws Throwable {
        System.err.println("around before");
        pjp.proceed();
        System.err.println("around after");
    }

}
```

### 启动类

```java
package top.simba1949;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

/**
 * @Author Theodore
 * @Date 2020/1/19 15:44
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
  port: 8082
```

