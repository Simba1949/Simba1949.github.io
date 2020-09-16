# HibernateValidator学习笔记

## 前言

版本说明

```properties
hibernate-validator=6.1.5.Final
```

相关链接：

* HibernateValidator 官网：http://hibernate.org/validator/
* HibernateValidator 官方文档：http://hibernate.org/validator/documentation/getting-started/
* HibernateValidator maven 地址：https://mvnrepository.com/artifact/org.hibernate.validator/hibernate-validator

## 常用注解

***注解中有个共同的属性 message，可以自定义错误信息***

| 注解                            | 说明                                                         |
| ------------------------------- | ------------------------------------------------------------ |
| @NotNull                        | 值不能为空                                                   |
| @Null                           | 值必须为空                                                   |
| @Pattern(regex=)                | 字符串必须匹配正则表达式                                     |
| @Size(min=, max=)               | 集合元素的数量必须在min和max之间                             |
| @Email                          | 字符串必须是Email地址                                        |
| @Length(min=,max=)              | 检查字符串的长度                                             |
| @NotBlank                       | 检查字符串不为null，并且长度大于0。                          |
| @Range(min=,max=)               | 数字必须大于等于min，小于等于max                             |
| @SafeHtml                       | 字符串是安全的html                                           |
| @URL                            | 字符串是合法的URL                                            |
| @AssertFalse                    | 值必须是false；支持的数据类型：Boolean`， `boolean           |
| @AssertTrue                     | 值必须是true；支持的数据类型：Boolean`， `boolean            |
| @DecimalMax(value=, inclusive=) | 值必须小于等于(inclusive=true)/小于(inclusive=false) value 属性指定的值；<br>支持的数据类型：`BigDecimal`，`BigInteger`，`CharSequence`，`byte`，`short`，`int`，`long`和原始类型的相应的包装； |
| @DecimalMin(value=, inclusive=) | 值必须大于等于(inclusive=true)/大于(inclusive=false) value 属性指定的值；<br> 支持的数据类型：`BigDecimal`，`BigInteger`，`CharSequence`，`byte`，`short`，`int`，`long`和原始类型的相应的包装； |
| @Digits(integer=, fraction=)    | 数字格式检查，integer指定整数部分的最大长度，fraction指定小数部分的最大长度<br>支持的数据类型：``BigDecimal`，`BigInteger`，`CharSequence`，`byte`，`short`，`int`，`long`和原始类型的相应的包装； |
| @Future                         | 值必须是未来的日期                                           |
| @FutureOrPresent                | 检查日期是现在还是将来                                       |
| @Past                           | 值必须是过去的日期                                           |
| @PastOrPresent                  | 检查带注释的日期是过去还是现在                               |
| @Max(value=)                    | 值必须小于等于value指定的值。                                |
| @Min(value=)                    | 值必须大于等于value指定的值                                  |
| @Negative                       | 检查元素是否严格为负。零值被视为无效。<br>支持的数据类型：`BigDecimal`，`BigInteger`，`byte`，`short`，`int`，`long`和原始类型的相应的包装 |
| @NegativeOrZero                 | 检查元素是负数或者零。                                       |
| @Positive                       | 检查元素是否严格为正。零值被视为无效。                       |
| @PositiveOrZero                 | 检查元素是正数还是零。                                       |

## 核心pom依赖

```xml
<!-- https://mvnrepository.com/artifact/org.hibernate.validator/hibernate-validator -->
<dependency>
    <groupId>org.hibernate.validator</groupId>
    <artifactId>hibernate-validator</artifactId>
    <version>6.1.5.Final</version>
</dependency>
<!-- https://mvnrepository.com/artifact/org.glassfish/jakarta.el -->
<dependency>
    <groupId>org.glassfish</groupId>
    <artifactId>jakarta.el</artifactId>
    <version>3.0.3</version>
</dependency>
<!-- https://mvnrepository.com/artifact/javax.el/javax.el-api -->
<dependency>
    <groupId>javax.el</groupId>
    <artifactId>javax.el-api</artifactId>
    <version>3.0.0</version>
</dependency>
```

## 校验 SpringMVC 入参校验

1. 在实体类字段添加校验注解
2. 在controller层添加@Valid 注解使之生效

```java
package top.simba1949.controller;

import lombok.extern.slf4j.Slf4j;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;
import top.simba1949.common.Person;

import javax.validation.Valid;

/**
 * @Author Theodore
 * @Date 2019/12/2 17:50
 */
@Slf4j
@RestController
@RequestMapping("person")
public class PersonController {
    /**
     * 添加注解 @Valid，才能使实体类Person的校验注解生效
     * @param person
     * @return
     */
    @PostMapping
    public String insert(@Valid @RequestBody Person person){
        log.debug("person is {}", person);
        return "SUCCESS";
    }
}
```

## 校验非 SpringMVC 入参校验

### pom.xml 依赖

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>top.simba1949</groupId>
    <artifactId>hibernate-validator-learn</artifactId>
    <version>1.0-SNAPSHOT</version>

    <dependencies>
        <!-- https://mvnrepository.com/artifact/org.hibernate.validator/hibernate-validator -->
        <dependency>
            <groupId>org.hibernate.validator</groupId>
            <artifactId>hibernate-validator</artifactId>
            <version>6.1.5.Final</version>
        </dependency>
        <!-- https://mvnrepository.com/artifact/org.glassfish/jakarta.el -->
        <dependency>
            <groupId>org.glassfish</groupId>
            <artifactId>jakarta.el</artifactId>
            <version>3.0.3</version>
        </dependency>
        <!-- https://mvnrepository.com/artifact/javax.el/javax.el-api -->
        <dependency>
            <groupId>javax.el</groupId>
            <artifactId>javax.el-api</artifactId>
            <version>3.0.0</version>
        </dependency>
        <!-- https://mvnrepository.com/artifact/org.projectlombok/lombok -->
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <version>1.18.12</version>
            <scope>provided</scope>
        </dependency>
        <!-- https://mvnrepository.com/artifact/com.google.guava/guava -->
        <dependency>
            <groupId>com.google.guava</groupId>
            <artifactId>guava</artifactId>
            <version>29.0-jre</version>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <!--https://mvnrepository.com/artifact/org.apache.maven.plugins/maven-compiler-plugin-->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.8.1</version>
                <configuration>
                    <source>1.8</source>
                    <target>1.8</target>
                    <encoding>UTF-8</encoding>
                </configuration>
            </plugin>
        </plugins>
    </build>
</project>
```

### 参数校验

#### User.java

```java
package top.simba1949.model;

import lombok.Data;

import javax.validation.constraints.NotNull;
import javax.validation.constraints.Size;

/**
 * @author Anthony
 * @date 2020/9/15 20:04
 */
@Data
public class User {
    @NotNull(message = "id不能为空")
    private Integer id;
    @Size(min = 2, max = 8, message = "用户名必须在2~8个字符区间内")
    @NotNull(message = "用户名必须不能为空")
    private String username;
}
```

#### 参数校验测试

```java
package top.simba1949;

import top.simba1949.model.User;

import javax.validation.ConstraintViolation;
import javax.validation.Validation;
import javax.validation.Validator;
import javax.validation.ValidatorFactory;
import java.util.Set;

/**
 * @author Anthony
 * @date 2020/9/15 19:22
 */
public class Application {
    public static void main(String[] args) {
        User user = new User();
        user.setId(1);
        user.setUsername("123456789");
        // Validator从中检索对象ValidatorFactory。
        // 一个Validator 实例是线程安全的，可重复使用多次。
        // 因此，它可以安全地存储在静态字段中，并在测试方法中用于验证不同的实例。
        ValidatorFactory factory = Validation.buildDefaultValidatorFactory();
        Validator validator = factory.getValidator();
        Set<ConstraintViolation<User>> violationSet = validator.validate(user);

        if (!violationSet.isEmpty()){
            throw new RuntimeException(violationSet.iterator().next().getMessage());
        }
    }
}
```

### 分组校验

#### 定义分组接口

```java
package top.simba1949.model;

/**
 * @author Anthony
 * @date 2020/9/15 22:41
 */
public interface RoleInsert {
}
```

```java
package top.simba1949.model;

/**
 * @author Anthony
 * @date 2020/9/15 22:41
 */
public interface RoleUpdate {
}
```

#### Role

```java
package top.simba1949.model;

import lombok.Data;

import javax.validation.constraints.NotNull;
import java.time.LocalDateTime;

/**
 * @author Anthony
 * @date 2020/9/15 22:34
 */
@Data
public class Role {
    /**
     *  groups 定义分组
     */
    @NotNull(message = "id不能为空", groups = {RoleUpdate.class})
    private Integer id;
    @NotNull(message = "roleName不能为空", groups = {RoleInsert.class, RoleUpdate.class})
    private String roleName;
    @NotNull(message = "gmtCreate不能为空", groups = {RoleInsert.class})
    private LocalDateTime gmtCreate;
    @NotNull(message = "gmtModify不能为空", groups = {RoleUpdate.class})
    private LocalDateTime gmtModify;
}
```

#### 参数校验测试

```java
package top.simba1949;

import top.simba1949.model.*;

import javax.validation.ConstraintViolation;
import javax.validation.Validation;
import javax.validation.Validator;
import javax.validation.ValidatorFactory;
import java.time.LocalDateTime;
import java.util.Set;

/**
 * @author Anthony
 * @date 2020/9/15 19:22
 */
public class Application {
    public static void main(String[] args) {
        Role role = new Role();
        role.setId(1);
        role.setGmtModify(LocalDateTime.now());
        ValidatorFactory factory = Validation.buildDefaultValidatorFactory();
        Validator validator = factory.getValidator();
        // 分组校验
        Set<ConstraintViolation<Role>> violationSet = validator.validate(role, RoleUpdate.class);
        if (!violationSet.isEmpty()){
            throw new RuntimeException(violationSet.iterator().next().getMessage());
        }
    }
}
```

### 自定义约束校验

要创建自定义约束，需要执行以下三个步骤：

1. 自定义注解并指定校验逻辑类
2. 自定义校验逻辑类，实现 `ConstraintValidator<A extends Annotation, T>` 接口，填写具体的校验逻辑
3. 和 `HibernateValidator` 原生注解一样使用即可

#### 自定义注解并指定校验逻辑类

```java
package top.simba1949;

import javax.validation.Constraint;
import javax.validation.Payload;
import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

/**
 * @author Anthony
 * @date 2020/9/15 22:54
 */
@Target(value = {ElementType.FIELD, ElementType.METHOD})
@Retention(value = RetentionPolicy.RUNTIME)
@Constraint(validatedBy = EmailConstraintValidator.class)
public @interface EmailValidation {
    // message()、groups()、payload() 必须存在

    String message() default "";

    Class<?>[] groups() default { };

    Class<? extends Payload>[] payload() default { };
}
```

#### 自定义校验逻辑类

```java
package top.simba1949;

import javax.validation.ConstraintValidator;
import javax.validation.ConstraintValidatorContext;

/**
 * @author Anthony
 * @date 2020/9/15 22:55
 */
public class EmailConstraintValidator implements ConstraintValidator<EmailValidation, String> {
    /**
     * 初始化
     * @param constraintAnnotation
     */
    @Override
    public void initialize(EmailValidation constraintAnnotation) {
        System.out.println("init");
    }

    @Override
    public boolean isValid(String value, ConstraintValidatorContext context) {
        if (null == value || value.length() == 0){
            return false;
        }
        final String emailFormat = "\\w[-\\w.+]*@([A-Za-z0-9][-A-Za-z0-9]+\\.)+[A-Za-z]{2,14}";
        return value.matches(emailFormat);
    }
}
```

#### 参数校验测试

```java
package top.simba1949;

import top.simba1949.model.User;

import javax.validation.ConstraintViolation;
import javax.validation.Validation;
import java.util.Set;

/**
 * @author Anthony
 * @date 2020/9/15 19:22
 */
public class Application {
    public static void main(String[] args) {
        User user = new User();
        user.setId(1);
        user.setUsername("username");
        Set<ConstraintViolation<User>> violationSet = Validation.buildDefaultValidatorFactory().getValidator().validate(user);
        if (!violationSet.isEmpty()){
            throw new RuntimeException(violationSet.iterator().next().getMessage());
        }
    }
}
```



