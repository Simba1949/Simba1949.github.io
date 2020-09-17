# Jackson学习笔记

## 前言

> `jackson-databind` 依赖 `jackson-core` 和 `jackson-annotations` ，所以只需要依赖 `jackson-databind` 即可

版本说明

```properties
jackson-databind=2.11.2
```

相关链接：

* jackson-databind maven 地址：https://mvnrepository.com/artifact/com.fasterxml.jackson.core/jackson-databind
* jackson github 地址：https://github.com/FasterXML/jackson
* jackson-datatype-jsr310 maven 地址：https://mvnrepository.com/artifact/com.fasterxml.jackson.datatype/jackson-datatype-jsr310
* jackson-lombok maven 地址：https://mvnrepository.com/artifact/com.xebia/jackson-lombok
* jackson-lombok github 地址：https://github.com/xebia/jackson-lombok

## Jackson 核心模块

核心模块是扩展（模块）所基于的基础。

* jackson-core ：定义了低级流API，并包括特定于JSON的实现
* jackson-annotations ：包含标准的Jackson注释
* jackson-databind ：在`streaming`包上实现了数据绑定（和对象序列化）支持；它依赖于`streaming`和`annotations`包

## Jackson 入门

### 核心依赖

```xml
<!-- https://mvnrepository.com/artifact/com.fasterxml.jackson.core/jackson-databind -->
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-databind</artifactId>
    <version>2.11.2</version>
</dependency>
<!-- Add-on module to support JSR-310 (Java 8 Date & Time API) data types. -->
<!-- https://mvnrepository.com/artifact/com.fasterxml.jackson.datatype/jackson-datatype-jsr310 -->
<dependency>
    <groupId>com.fasterxml.jackson.datatype</groupId>
    <artifactId>jackson-datatype-jsr310</artifactId>
    <version>2.11.2</version>
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
    <artifactId>jackson-learn</artifactId>
    <version>1.0-SNAPSHOT</version>

    <dependencies>
        <!-- https://mvnrepository.com/artifact/org.projectlombok/lombok -->
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <version>1.18.12</version>
            <scope>provided</scope>
        </dependency>
        <!-- https://mvnrepository.com/artifact/com.fasterxml.jackson.core/jackson-databind -->
        <dependency>
            <groupId>com.fasterxml.jackson.core</groupId>
            <artifactId>jackson-databind</artifactId>
            <version>2.11.2</version>
        </dependency>
        <!-- Add-on module to support JSR-310 (Java 8 Date & Time API) data types. -->
        <!-- https://mvnrepository.com/artifact/com.fasterxml.jackson.datatype/jackson-datatype-jsr310 -->
        <dependency>
            <groupId>com.fasterxml.jackson.datatype</groupId>
            <artifactId>jackson-datatype-jsr310</artifactId>
            <version>2.11.2</version>
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

### User 

```java
package top.simba1949.model;

import com.fasterxml.jackson.annotation.JsonFormat;
import com.fasterxml.jackson.databind.annotation.JsonDeserialize;
import com.fasterxml.jackson.databind.annotation.JsonSerialize;
import com.fasterxml.jackson.datatype.jsr310.deser.LocalDateTimeDeserializer;
import com.fasterxml.jackson.datatype.jsr310.ser.LocalDateTimeSerializer;
import lombok.Data;

import java.io.Serializable;
import java.math.BigDecimal;
import java.time.LocalDateTime;
import java.util.Date;

/**
 * @author Anthony
 * @date 2020/9/17 10:35
 */
@Data
public class User implements Serializable {
    private static final long serialVersionUID = 6561948355109969817L;

    private Integer id;
    private String username;
    private BigDecimal price;
    /**
     * 日期序列化与反序列化指定格式
     */
    @JsonFormat(pattern = "yyyy-MM-dd HH:mm:ss")
    private Date birthday;
    /**
     * JDK8 日期需要引入 jackson-datatype-jsr310，并指定序列化方式
     */
    @JsonDeserialize(using = LocalDateTimeDeserializer.class)
    @JsonSerialize(using = LocalDateTimeSerializer.class)
    private LocalDateTime time;
}
```

### 测试

```java
package top.simba1949;

import com.fasterxml.jackson.databind.ObjectMapper;
import top.simba1949.model.User;

import java.io.IOException;
import java.math.BigDecimal;
import java.time.LocalDateTime;
import java.util.Date;

/**
 * @author Anthony
 * @date 2020/9/17 10:21
 */
public class Application {
    public static void main(String[] args) throws IOException {
        User user = new User();
        user.setId(1);
        user.setUsername("username");
        user.setPrice(new BigDecimal("0.01"));
        user.setBirthday(new Date());
        user.setTime(LocalDateTime.now());

        String userStr =  new ObjectMapper().writer().writeValueAsString(user);
        System.out.println(userStr);
        User deUserStr =  new ObjectMapper().reader().readValue(userStr, User.class);
        System.out.println(deUserStr);
    }
}
```

## Jackson 使用

`ObjectWriter objectWriter = new ObjectMapper().writer()` 

1. 通过 `ObjectMapper` 获取 `ObjectWriter` 对象，通过 `writeValue` 方式序列化 `Java` 对象；

   ```java
   ObjectWriter objectWriter = new ObjectMapper().writer();
   // 序列化成字符串
   objectWriter.writeValueAsString();
   // 序列化成字节数组
   objectWriter.writeValueAsBytes();
   // JsonGenerator
   // File
   // OutputStream
   // Writer
   // DataOutput
   objectWriter.writeValue();
   ```

2. 通过 `ObjectMapper` 获取 `ObjectReader` 对象，通过 `readValue` 方式从不同的数据源（JsonParser，InputStream，Reader，String，字节数组，File，URL，JsonNode，DataInput）反序列化 `Java` 对象

   ```java
   ObjectReader objectReader = new ObjectMapper().reader();
   User deUserStr = objectReader.readValue(userStr, User.class);
   ```

### 序列化与反序列化配置

在调用 `writeValue` 或调用 `readValue` 方法之前，往往需要设置 `ObjectMapper` 的相关配置信息。

```java
ObjectMapper objectMapper = new ObjectMapper();
// 在序列化时忽略值为 null 的属性
objectMapper.setSerializationInclusion(JsonInclude.Include.NON_NULL);
// 在反序列化时忽略在 json 中存在但 Java 对象不存在的属性
objectMapper.configure(DeserializationFeature.FAIL_ON_UNKNOWN_PROPERTIES, false);
```

或者

```java
String userStr = new ObjectMapper()
                // 在序列化时忽略值为 null 的属性
                .setSerializationInclusion(JsonInclude.Include.NON_NULL)
                .writeValueAsString(user);

User deUser = new ObjectMapper()
                // 在反序列化时忽略在 json 中存在但 Java 对象不存在的属性
                .configure(DeserializationFeature.FAIL_ON_UNKNOWN_PROPERTIES, false)
                .reader()
                .readValue(userStrDiy, User.class);
```

### Jackson 注解的使用

| 注解               | 用法                                                         |
| ------------------ | ------------------------------------------------------------ |
| @JsonProperty      | 用于属性，把属性的名称序列化时转换为另外一个名称。示例：  @JsonProperty("birth_ d ate")  private Date birthDate; |
| @JsonFormat        | 用于属性或者方法，把属性的格式序列化时转换成指定的格式。示例：  @JsonFormat(timezone = "GMT+8", pattern = "yyyy-MM-dd HH:mm:ss")  public Date getBirthDate() |
| @JsonPropertyOrder | 用于类， 指定属性在序列化时 json 中的顺序 ， 示例：  @JsonPropertyOrder({ "birth_Date", "name" }) |
| @JsonCreator       | 用于构造方法，和 @JsonProperty 配合使用，适用有参数的构造方法。 示例：  @JsonCreator  public Person(@JsonProperty("name")String name) {…} |
| @JsonAnySetter     | 用于属性或者方法，设置未反序列化的属性名和值作为键值存储到 map 中  @JsonAnySetter  public void set(String key, Object value) {  map.put(key, value);  } |
| @JsonAnyGetter     | 用于方法 ，获取所有未序列化的属性  public Map<String, Object> any() { return map; } |

## 反序列化

### 反序列化为对象

```java
User user = User.builder().id(1).username("name1").birthday(new Date()).price(new BigDecimal(1)).time(LocalDateTime.now()).build();
// 序列化
String jsonStr = new ObjectMapper().writer().writeValueAsString(user);
// 反序列化为对象
User deUser = new ObjectMapper().reader().forType(User.class).readValue(jsonStr);
```

### 反序列化为数组

```java
List<User> userList = new ArrayList<>();
for (int i = 0; i < 5; i++) {
    User user = User.builder().id(i).username("name" + i).birthday(new Date()).price(new BigDecimal(i)).time(LocalDateTime.now()).build();
    userList.add(user);
}
// 序列化
String jsonStr = new ObjectMapper().writer().writeValueAsString(userList);
// 从JSON数组字符串中读取对象数组
User[] deUserArr = new ObjectMapper().reader().forType(User[].class).readValue(jsonStr);
for (User user : deUserArr) {
    System.out.println(user);
}
```

### 反序列化为List集合

```java
List<User> userList = new ArrayList<>();
for (int i = 0; i < 5; i++) {
    User user = User.builder().id(i).username("name" + i).birthday(new Date()).price(new BigDecimal(i)).time(LocalDateTime.now()).build();
    userList.add(user);
}
// 序列化
String jsonStr = new ObjectMapper().writer().writeValueAsString(userList);
// 从JSON数组字符串中读取对象列表
List<User> deUserList = new ObjectMapper().reader().forType(new TypeReference<List<User>>() {}).readValue(jsonStr);
deUserList.forEach(item-> System.out.println(item));
```

### 反序列化为Set集合

```java
List<User> userList = new ArrayList<>();
for (int i = 0; i < 5; i++) {
    User user = User.builder().id(i).username("name" + i).birthday(new Date()).price(new BigDecimal(i)).time(LocalDateTime.now()).build();
    userList.add(user);
}
// 序列化
String jsonStr = new ObjectMapper().writer().writeValueAsString(userList);
// 从JSON数组字符串中读取对象列表
Set<User> deUserSet = new ObjectMapper().reader().forType(new TypeReference<Set<User>>() {}).readValue(jsonStr);
deUserSet.forEach(item-> System.out.println(item));
```

### 反序列化为Map集合

```java
User user = User.builder().id(1).username("name1").birthday(new Date()).price(new BigDecimal(1)).time(LocalDateTime.now()).build();
// 序列化
String jsonStr = new ObjectMapper().writer().writeValueAsString(user);
// 反序列化为Map集合
Map<String, Object> deMap = new ObjectMapper().reader().forType(Map.class).readValue(jsonStr);
```

