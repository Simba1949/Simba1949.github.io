# knife4j学习笔记

## 前言

博客书

版本说明

```properties
knife4j-spring-boot-starter=2.0.2
```

相关链接

* 官方文档：https://doc.xiaominfo.com/guide/
* maven 地址：https://mvnrepository.com/artifact/com.github.xiaoymin/knife4j-spring-boot-starter
* github 地址：https://github.com/xiaoymin/swagger-bootstrap-ui

## knife4j 简介

https://doc.xiaominfo.com/guide/

### 增强注解

#### @ApiSort

用于Controller排序（从小到大排序）

```java
/**
 * @Author Theodore
 * @Date 2020/3/3 17:48
 */
@ApiSort(1)
@Api(tags = {"用户模块"})
@RestController
@RequestMapping("user")
public class UserController {}
```

#### @ApiOperationSupport

用于接口排序、接口开发者、需要忽略的参数等（从小到大排序）

```java
@GetMapping("user-str2")
@ApiOperation(value = "获取user-str2")
@ApiOperationSupport(order = 2, author = "theodore", ignoreParameters = {"id", "role.id"})
public String getStr2(User user){
    return "user-str2";
}
```

忽略的规则如下：

- JSON数据忽略参数：ignoreParameters = {"user.id", "user.role.id"}
- 非JSON数据忽略参数：ignoreParameters = {"id", "role.id"} 

### 生产环境屏蔽接口文档

如果使用SpringBoot框架，只需在`application.properties`或者`application.yml`配置文件中配置：

```properties
knife4j.production=true
```

配置此属性后，所有资源都会屏蔽输出。

允许开发者在配置文件中配置一个静态的用户名和密码，当对接者访问 `Swagger` 页面时，输入此配置的用户名和密码后才能访问 Swagger 文档页面，如果您使用SpringBoot开发，则只需在相应的`application.properties`或者`application.yml`中配置如下：

```properties
## 以下配置不建议和 knife4j.production=true 一起使用，一起使用后下面配置虽然生效但是无法访问
## 开启Swagger的Basic认证功能,默认是false
knife4j.basic.enable=true
## Basic认证用户名
knife4j.basic.username=zhangsan
## Basic认证密码
knife4j.basic.password=123
```

## 实战演练

### pom 核心依赖

```xml
<!-- https://mvnrepository.com/artifact/com.github.xiaoymin/knife4j-spring-boot-starter -->
<dependency>
    <groupId>com.github.xiaoymin</groupId>
    <artifactId>knife4j-spring-boot-starter</artifactId>
    <version>${knife4j-spring-boot-starter.version}</version>
</dependency>
```

### 在 springboot 项目中添加配置

```java
package top.simba1949.config;

import com.github.xiaoymin.knife4j.spring.annotations.EnableKnife4j;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Import;
import springfox.bean.validators.configuration.BeanValidatorPluginsConfiguration;
import springfox.documentation.builders.ApiInfoBuilder;
import springfox.documentation.builders.PathSelectors;
import springfox.documentation.builders.RequestHandlerSelectors;
import springfox.documentation.service.ApiInfo;
import springfox.documentation.service.Contact;
import springfox.documentation.spi.DocumentationType;
import springfox.documentation.spring.web.plugins.Docket;
import springfox.documentation.swagger2.annotations.EnableSwagger2;

import java.util.function.Predicate;

import static com.google.common.base.Predicates.or;
import static springfox.documentation.builders.PathSelectors.regex;

/**
 * swagger-bootstrap-ui 默认访问地址是：http://${host}:${port}/doc.html
 * 注意：使用权限控制需要释放以下三个接口：
 * /swagger-resources : Swagger的分组接口
 * /v2/api-docs?group=groupName : Swagger的具体分组实例接口,返回该分组下所有接口相关的Swagger信息
 * /v2/api-docs-ext?group=groupName : 该接口是SwaggerBootstrapUi提供的增强接口地址,如不使用UI增强,则可以忽略该接口
 *
 * @Author Theodore
 * @Date 2020/3/3 17:33
 */
@Configuration
@EnableSwagger2
@EnableKnife4j
@Import(BeanValidatorPluginsConfiguration.class)
public class Swagger2Conf {

    /**
     * springfox.documentation.spring.web.plugins.Docket 用于构建 Springfox 框架
     * @return
     */
    @Bean
    public Docket commonDocket() {
        return new Docket(DocumentationType.SWAGGER_2)
            // 如果存在多个 Docket ，需要进行分组命名
            .groupName("commonDocket-api")
            // 设置 meta 信息
            .apiInfo(apiInfo("标题：通用开发接口文档", "描述：通用开发接口文档"))
            // 启动 api 选择构建器
            .select()
            // 哪些包路径下属于通用开发接口文档
            .apis(RequestHandlerSelectors.basePackage("top.simba1949.controller.common"))
            // 路径筛选 PathSelectors.any() 匹配所有接口，也可以自定义匹配哪些接口
            .paths(PathSelectors.any())
            // 构建
            .build();
    }

    /**
     * 自定义匹配哪些接口
     * @return
     */
    private Predicate<String> postPaths() {
        return or(
            regex("/api/posts.*"),
            regex("/api/comments.*")
        );
    }

    @Bean
    public Docket companyDocket() {
        return new Docket(DocumentationType.SWAGGER_2)
            // 如果存在多个 Docket ，需要进行分组命名
            .groupName("companyDocket-api")
            // 设置 meta 信息
            .apiInfo(apiInfo("标题：公司开发接口文档", "描述：公司开发接口文档"))
            // 启动 api 选择构建器
            .select()
            // 哪些包路径下属于公司开发接口文档
            .apis(RequestHandlerSelectors.basePackage("top.simba1949.controller.company"))
            // 路径筛选
            .paths(PathSelectors.any())
            // 构建
            .build();
    }

    private ApiInfo apiInfo(String title, String description) {
        // 名称，站点，邮箱地址
        Contact contact = new Contact("simba1949", "https://simba1949.blog.csdn.net", "simba1949@outlook.com");
        return new ApiInfoBuilder()
            // 标题
            .title(title)
            // 描述
            .description(description)
            // 服务条款链接
            .termsOfServiceUrl("http://hantsy.blogspot.com")
            // 联系人
            .contact(contact)
            // 认证许可
            .license("Apache License Version 2.0")
            // 认证许可链接
            .licenseUrl("https://github.com/springfox/springfox/blob/master/LICENSE")
            // 版本
            .version("2.0")
            .build();
    }
}
```

### Controller 代码信息

```java
package top.simba1949.controller.common;

import com.github.xiaoymin.knife4j.annotations.ApiOperationSupport;
import com.github.xiaoymin.knife4j.annotations.ApiSort;
import io.swagger.annotations.Api;
import io.swagger.annotations.ApiOperation;
import org.springframework.web.bind.annotation.*;
import top.simba1949.common.User;

/**
 * @Author Theodore
 * @Date 2020/3/3 17:48
 */
@ApiSort(1)
@Api(tags = {"用户模块"})
@RestController
@RequestMapping("user")
public class UserController {
    /**
     * 非JSON数据忽略参数：ignoreParameters = {"id", "role.id"}
     * @param user
     * @return
     */
    @GetMapping("user-str1")
    @ApiOperation(value = "获取user-str1")
    @ApiOperationSupport(order = 1, author = "theodore", ignoreParameters = {"id", "role.id"})
    public String getStr1(User user){
        return "user-str1";
    }

    /**
     * JSON数据忽略参数：ignoreParameters = {"user.id", "user.role.id"}
     * @param user
     * @return
     */
    @PostMapping("user-str2")
    @ApiOperation(value = "获取user-str2")
    @ApiOperationSupport(order = 2, ignoreParameters = {"user.id", "user.role.id"})
    public String getStr2(@RequestBody User user){
        return "user-str2";
    }

    @GetMapping("user-str3")
    @ApiOperation(value = "获取user-str3")
    @ApiOperationSupport(order = 3)
    public String getStr3(){
        return "user-str3";
    }

    @GetMapping("user-str4")
    @ApiOperation(value = "获取user-str4")
    @ApiOperationSupport(order = 4)
    public String getStr4(){
        return "user-str4";
    }
}
```


