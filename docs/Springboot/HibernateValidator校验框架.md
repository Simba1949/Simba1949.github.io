# HibernateValidator校验框架

## 前言

博客书

版本说明

```properties
platform-bom=Cairo-SR7
```

相关链接

* spring io 官网：https://spring.io/projects/platform
* jakarta maven 地址：https://mvnrepository.com/artifact/jakarta.validation/jakarta.validation-api

注：`spring-boot-starter` 和 `spring-boot-starte-web` 都会自动依赖 `jakarta.validation-api` 的包

## 常用注解

***注解中有个共同的属性 message，可以自定义错误信息***

| 注解                                         | 说明                                                         |
| -------------------------------------------- | ------------------------------------------------------------ |
| @NotNull                                     | 值不能为空                                                   |
| @Null                                        | 值必须为空                                                   |
| @Pattern(regex=)                             | 字符串必须匹配正则表达式                                     |
| @Size(min=, max=)                            | 集合元素的数量必须在min和max之间                             |
| @CreditCardNumber(ignoreNonDigitCharacters=) | 字符串必须是信用卡号（按美国的标准验的）                     |
| @Email                                       | 字符串必须是Email地址                                        |
| @Length(min=,max=)                           | 检查字符串的长度                                             |
| @NotBlank                                    | 字符串必须有字符                                             |
| @Range(min=,max=)                            | 数字必须大于等于min，小于等于max                             |
| @SafeHtml                                    | 字符串是安全的html                                           |
| @URL                                         | 字符串是合法的URL                                            |
| @AssertFalse                                 | 值必须是false                                                |
| @AssertTrue                                  | 值必须是true                                                 |
| @DecimalMax(value=, inclusive=)              | 值必须小于等于(inclusive=true)/小于(inclusive=false) value 属性指定的值 |
| @DecimalMin(value=, inclusive=)              | 值必须大于等于(inclusive=true)/大于(inclusive=false) value 属性指定的值 |
| @Digits(integer=, fraction=)                 | 数字格式检查，integer指定整数部分的最大长度，fraction指定小数部分的最大长度 |
| @Future                                      | 值必须是未来的日期                                           |
| @Past                                        | 值必须是过去的日期                                           |
| @Max(value=)                                 | 值必须小于等于value指定的值。                                |
| @Min(value=)                                 | 值必须大于等于value指定的值。                                |



## 实战演练

### HibernateValidator 校验框架的使用步骤

1. 在实体类字段添加校验注解
2. 在controller层添加@Valid 注解使之生效

### 实体类

```java
package top.simba1949.common;

import lombok.Data;

import javax.validation.constraints.NotNull;
import java.io.Serializable;

/**
 * @Author Theodore
 * @Date 2019/12/2 17:49
 */
@Data
public class Person implements Serializable {
    private static final long serialVersionUID = -76403129629930396L;

    private Integer id;
    private String username;
    @NotNull(message = "密码不能为空")
    private String password;
}
```

### Controller 层

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

## 自定义校验注解

### 自定义校验注解步骤

1. 自定义注解并指定校验逻辑类
2. 定义校验逻辑类，实现 `ConstraintValidator<A extends Annotation, T>` 接口，填写具体的校验逻辑
3. 和 `HibernateValidator` 原生注解一样使用即可

### 自定义注解

```java
package top.simba1949.constraint;

import javax.validation.Constraint;
import javax.validation.Payload;
import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

/**
 * @Target(value = {ElementType.FIELD, ElementType.METHOD}) 表示作用域
 * @Retention(value = RetentionPolicy.RUNTIME) 表示生效策略
 * @Constraint(validatedBy = ValidEmailHandler.class) 由哪个类进行验证
 *
 * @Author Theodore
 * @Date 2019/12/2 20:25
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

### 校验验逻辑类

```java
package top.simba1949.constraint;

import javax.validation.ConstraintValidator;
import javax.validation.ConstraintValidatorContext;

/**
 *
 * ConstraintValidator<EmailValidation, String> EmailValidation表示验证哪个注解；String表示用于哪种类型
 *
 * @Author Theodore
 * @Date 2019/12/2 20:32
 */
public class EmailConstraintValidator implements ConstraintValidator<EmailValidation, String> {
   /**
    * 初始化
    * @param constraint
    */
   @Override
   public void initialize(EmailValidation constraint) {
      System.out.println("init");
   }

   /**
    * 真正的校验逻辑
    * @param value 传入的值
    * @param context 注解的上下文
    * @return false 表示验证不通过，true 表示验证通过
    */
   @Override
   public boolean isValid(String value, ConstraintValidatorContext context) {
      final String emailFormat = "\\w[-\\w.+]*@([A-Za-z0-9][-A-Za-z0-9]+\\.)+[A-Za-z]{2,14}";
      return value.matches(emailFormat);
   }
}
```

