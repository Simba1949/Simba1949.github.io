# SpringBoot和SpringCloud项目基础pom

相关链接

* spring-boot 和 spring-cloud 版本兼容参考 ：https://spring.io/projects/spring-cloud
* spring-boot-dependencies ：https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-dependencies
* spring-cloud-dependencies：https://mvnrepository.com/artifact/org.springframework.cloud/spring-cloud-dependencies
* fastjson ：https://mvnrepository.com/artifact/com.alibaba/fastjson
* guava ：https://mvnrepository.com/artifact/com.google.guava/guava
* hutool-all ：https://mvnrepository.com/artifact/cn.hutool/hutool-all

## SprinBoot项目基础依赖

```xml
<properties>
    <!--spring-boot 和 spring-cloud 版本兼容参考 https://spring.io/projects/spring-cloud -->
    <main.class>top.simba1949.Application</main.class>
</properties>

<!-- 
    Inherit defaults from Spring Boot
    参考链接：https://docs.spring.io/spring-boot/docs/2.3.2.RELEASE/maven-plugin/reference/html/#using
        Maven 用户可以从 spring-boot-starter-parent 项目继承以获得合理的默认值。父项目提供以下功能：
        1.Java 1.8 是默认的编译器级别。
        2.UTF-8 源编码。
        3.从 spring-boot-dependenciesPOM 继承的依赖性管理部分，用于管理常见依赖性的版本。
        4.具有执行ID 的 repackage 目标的 repackage 执行。
    -->
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.2.9.RELEASE</version>
</parent>

<dependencies>
    <!-- spring-boot basic starter-->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-configuration-processor</artifactId>
        <optional>true</optional>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-actuator</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-devtools</artifactId>
        <scope>runtime</scope>
        <optional>true</optional> <!-- 表示依赖不会传递 -->
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-test</artifactId>
        <scope>test</scope>
    </dependency>
    <!-- spring-boot basic end-->

    <!-- tools starter-->
    <dependency>
        <groupId>org.projectlombok</groupId>
        <artifactId>lombok</artifactId>
    </dependency>
    <!-- tools end-->

    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
</dependencies>

<build>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
            <configuration>
                <!--main of springboot project-->
                <mainClass>${main.class}</mainClass>
                <!-- 如果没有该配置，devtools不会生效 -->
                <fork>true</fork>
                <!--将项目注册到linux服务上，可以通过命令开启、关闭以及伴随开机启动等功能-->
                <executable>true</executable>
            </configuration>
        </plugin>
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
```

## SpringCloud项目基础依赖

```xml

```

