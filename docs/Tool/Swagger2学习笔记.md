# Swagger2 学习笔记

## 前言

版本说明：

```properties
jdk=1.8.0_221
maven=3.6.1
springfox-swagger2=2.9.2
```

参考链接

* swagger2 官方 Java 开发文档： https://hantsy.gitbooks.io/build-a-restful-app-with-spring-mvc-and-angularjs/content/swagger.html 
* springfox-swagger2 maven 地址： https://mvnrepository.com/artifact/io.springfox/springfox-swagger2 
*  springfox-swagger-ui maven 地址：https://mvnrepository.com/artifact/io.springfox/springfox-swagger-ui 

## 相关链接

1. [Generating Swagger for a Jersey Project](https://github.com/swagger-api/swagger-core/wiki/Swagger-Core-Jersey-2.X-Project-Setup-1.5)
2. [Generating Swagger for Spring based APIs](https://hantsy.gitbooks.io/build-a-restful-app-with-spring-mvc-and-angularjs/content/swagger.html)
3. [Generating Swagger for PHP based APIs](https://github.com/zircote/swagger-php/blob/master/docs/Getting-started.md)

## 什么是 Swagger2

>  编写和维护接口文档是每个程序员的职责，根据 Swagger2 可以快速帮助我们编写最新的API接口文档，再也不用担心开会前仍忙于整理各种资料了，间接提升了团队开发的沟通效率。常用注解swagger通过注解表明该接口会生成文档，包括接口名、请求方法、参数、返回信息的等等。 

## 核心依赖

```xml
<swagger2.version>2.9.2</swagger2.version>
<!-- SpringFox Swagger UI -->
 <dependency>
     <groupId>io.springfox</groupId>
     <artifactId>springfox-swagger2</artifactId>
     <version>${swagger2.version}</version>
 </dependency>
 <dependency>
     <groupId>io.springfox</groupId>
     <artifactId>springfox-swagger-ui</artifactId>
     <version>${swagger2.version}</version>
 </dependency>
```

## Swagger2 配置类

```java
package top.simba1949.config;

import com.google.common.base.Predicate;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import springfox.documentation.builders.ApiInfoBuilder;
import springfox.documentation.builders.PathSelectors;
import springfox.documentation.builders.RequestHandlerSelectors;
import springfox.documentation.service.ApiInfo;
import springfox.documentation.service.Contact;
import springfox.documentation.spi.DocumentationType;
import springfox.documentation.spring.web.plugins.Docket;
import springfox.documentation.swagger2.annotations.EnableSwagger2;

import static com.google.common.base.Predicates.or;
import static springfox.documentation.builders.PathSelectors.regex;

/**
 * 需要 @EnableSwagger2 开启 swagger2
 * 启动项目后访问 http://ip:port/swagger-ui.html 即可访问
 *
 * @Author Theodore
 * @Date 2019/10/23 18:34
 */
@Configuration
@EnableSwagger2
public class Swagger2Config {
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
        Contact contact = new Contact("simba1949", "https://blog.csdn.net/simba1949", "simba1949@outlook.com");
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

效果

![](https://github.com/simba1949/images/blob/master/FrameTool/swagger2/00-swagger2.png?raw=true)

## 常用注解

* @Api：修饰整个类，描述Controller的作用
* @ApiOperation：描述一个类的一个方法，或者说一个接口
* @ApiParam：单个参数描述
* @ApiModel：用对象来接收参数
* @ApiModelProperty：用对象接收参数时，描述对象的一个字段
* @ApiIgnore：使用该注解忽略这个API，用于类或者方法上
* @ApiImplicitParam：一个请求参数，用于方法上
* @ApiImplicitParams：多个请求参数 ，包含多个 @ApiImplicitParam，用于方法上

### 注意

@ApiModel 和 @ApiImplicitParams 不推荐同时使用

### @Api

**强制：tags(数组长度建议为 1)**

```java
@Api(
        tags = {"公司管理"}, // 字符串数组；用于说明该类的作用(强制)
        authorizations = {}, // 字符串数组；用于权限配置
        consumes = "", // 字符串, 可选值："application/json, application/xml"；说明接收协议类型
        hidden = false, // 布尔类型；是否隐藏该类api
        produces = "", // 字符串，可选值："application/json, application/xml"；说明输出协议类型
        protocols = "" // 字符串，可选值：http, https, ws, wss；说明支持的协议类型
)
```

### @ApiOperation

**强制：value**

```java
    @ApiOperation(
            authorizations = {}, // 字符串数组；用于权限配置
            code = 200, // int；响应的HTTP状态代码。
            consumes = "", // 字符串，可选值"application/json, application/xml"，接收协议类型
            hidden = false, // 布尔类型；是否隐藏该接口
            httpMethod = "", // 字符串，可选值 "GET", "HEAD", "POST", "PUT", "DELETE", "OPTIONS" and "PATCH"；接口请求方式
            notes = "操作的详细描述。", // 字符串；操作的详细描述。(强制)
            produces = "", // 字符串，可选值："application/json, application/xml"；说明输出协议类型
            protocols = "", // 字符串，可选值：http, https, ws, wss；说明支持的协议类型
            response = String.class, // Class 类型；返回类型
            responseContainer = "", // 字符串，可选值"List", "Set" or "Map"；声明包装响应的容器。
            responseHeaders = {}, // ResponseHeader数组；响应头设置
            value = "接口描述或者摘要" // 字符串；接口描述或者摘要
    )
```

### @ApiParam

**强制：name、value**

```java
@ApiParam(
    name = "companyName", // 参数名
    defaultValue = "ali", // 默认值
    example = "示例", // 示例值
    value = "公司名称", // 参数简要说明
    hidden = false,  // 是否隐藏改参数
    required = false // 是否必须输入
) String companyName
```

### @ApiModel & @ApiModelProperty

@ApiModel 强制：value、description

 @ApiModelProperty 强制：name、value、dataType

```java
@ApiModel(
        value = "类名",
        description = "类的详细描述",
        parent = Object.class // 为模型提供超类，以允许描述继承。
)

@ApiModelProperty(
    dataType = "int", // 数据类型
    hidden = false, // 是否隐藏
    name = "id", // 属性名称
    required = true, // 是否必须
    value = "主键" // 属性描述
)
```

### @ApiIgnore

**强制：value**

```java
// 使用该注解忽略这个API，用于类或者方法上
@ApiIgnore(value = "填写忽略该api的原因")
```

### @ApiImplicitParam & @ApiImplicitParams

**强制：@ApiImplicitParam ：name、value、dataTypeClass（dataType）**

**强制：@ApiImplicitParams ：value**

> @ApiImplicitParam ：用在请求的方法上，表示一个参数说明
>
> @ApiImplicitParams ：用在请求的方法上，表示一组参数说明



```java
    @ApiImplicitParam(
            dataType = "int", // 字符串；入参类型
            dataTypeClass = Integer.class, // Class 类型；入参类型，如果存在会覆盖 dataType
            defaultValue = "", // 字符串；入参默认值
            name = "", // 字符串； 入参名
            required = true, // 布尔类型；是否必传
            value = "对参数的描述", // 字符串类型；表示对参数的描述
            paramType = "" // 字符串类型，可选值path/query/body/header/form；表示参数存在位置
    )
```

```java
// value 是 @ApiImplicitParam 的数组
@ApiImplicitParams(
    value = {
        @ApiImplicitParam(
            dataType = "int", // 字符串；入参类型
            name = "id", // 字符串； 入参名
            value = "公司id值", // 字符串类型；表示对参数的描述
        ),
        @ApiImplicitParam(
            dataType = "string", // 字符串；入参类型
            name = "companyName", // 字符串； 入参名
            value = "公司名称", // 字符串类型；表示对参数的描述
        )
    }
)
```


