# SpringBoot项目多环境、配置和依赖分离打包

## 前言

博客书

版本说明

```properties
spring-boot=2.2.5.RELEASE
```

相关链接：

* 

## 实战说明

### pom 依赖

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>top.simba1949</groupId>
    <artifactId>spring-boot-package-project</artifactId>
    <version>1.0-SNAPSHOT</version>

    <properties>
        <finalName.text>spring-boot-package-project</finalName.text>
        <!-- https://mvnrepository.com/artifact/org.apache.maven.plugins/maven-resources-plugin -->
        <maven-resources-plugin.version>3.1.0</maven-resources-plugin.version>
        <!-- https://mvnrepository.com/artifact/org.apache.maven.plugins/maven-jar-plugin -->
        <maven.jar.plugin.version>3.2.0</maven.jar.plugin.version>
        <!-- https://mvnrepository.com/artifact/org.apache.maven.plugins/maven-dependency-plugin -->
        <maven.dependency.plugin.version>3.1.2</maven.dependency.plugin.version>
        <!--https://mvnrepository.com/artifact/org.springframework.cloud/spring-cloud-dependencies-->
        <spring-cloud-dependencies.version>Hoxton.RELEASE</spring-cloud-dependencies.version>
        <!--https://mvnrepository.com/artifact/com.alibaba/fastjson -->
        <fastjson.version>1.2.62</fastjson.version>
        <!--https://mvnrepository.com/artifact/com.google.guava/guava -->
        <guava.version>28.2-jre</guava.version>
        <!--https://mvnrepository.com/artifact/cn.hutool/hutool-all -->
        <hutool-all.version>5.1.3</hutool-all.version>
    </properties>

    <!--spring-boot 和 spring-cloud 版本兼容参考 https://spring.io/projects/spring-cloud -->
    <!-- Inherit defaults from Spring Boot -->
    <!--https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-starter-parent -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.2.5.RELEASE</version>
    </parent>

    <dependencyManagement>
        <dependencies>
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
        <!--Core starter, including auto-configuration support, logging and YAML-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter</artifactId>
        </dependency>
        <!-- spring-cloud-context，使 bootstrap.properties 配置文件生效 -->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-context</artifactId>
        </dependency>
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

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
    </dependencies>

    <build>
        <finalName>${finalName.text}</finalName>
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
            <!--The Resources Plugin handles the copying of project resources to the output directory.
                将配置文件输出到指定目录下
                当前项目config目录下>当前项目根目录下>类路径config目录下>类路径根目录下，
            -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-resources-plugin</artifactId>
                <version>${maven-resources-plugin.version}</version>
                <configuration>
                    <encoding>UTF-8</encoding>
                </configuration>
                <executions>
                    <execution>
                        <id>copy-resources</id>
                        <phase>package</phase>
                        <goals>
                            <goal>copy-resources</goal>
                        </goals>
                        <configuration>
                            <resources>
                                <resource>
                                    <directory>src/main/resources</directory>
                                    <!-- 开启filtering功能，不开启无法完成多环境打包 -->
                                    <filtering>true</filtering>
                                    <includes>
                                        <!-- 根据环境打包配置文件-->
                                        <include>**/*-${activatedProperties}.yml</include>
                                    </includes>
                                    <excludes>
                                        <exclude>**/application.yml</exclude>
                                    </excludes>
                                </resource>
                            </resources>
                            <outputDirectory>${project.build.directory}/config</outputDirectory>
                        </configuration>
                    </execution>
                </executions>
            </plugin>
            <!-- Provides utility goals to work with dependencies like copying, unpacking, analyzing, resolving and many more.
                pom依赖的jar包输出到指定的目录下
            -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-dependency-plugin</artifactId>
                <version>${maven.dependency.plugin.version}</version>
                <executions>
                    <execution>
                        <id>copy-dependencies</id>
                        <phase>package</phase>
                        <goals>
                            <goal>copy-dependencies</goal>
                        </goals>
                        <configuration>
                            <outputDirectory>${project.build.directory}/lib</outputDirectory>
                            <excludeTransitive>false</excludeTransitive>
                            <stripVersion>false</stripVersion>
                            <includeScope>runtime</includeScope>
                        </configuration>
                    </execution>
                </executions>
            </plugin>
            <!-- SpringBoot 打包插件，把 maven-jar-plugin 打成的jar包重新打成可运行jar包
                打包时去除第三方依赖
            -->
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <configuration>
                    <layout>ZIP</layout>
                    <!--重写包含依赖，包含不存在的依赖，jar里没有pom里的依赖 -->
                    <includes>
                        <include>
                            <groupId>non-exists</groupId>
                            <artifactId>non-exists</artifactId>
                        </include>
                    </includes>
                    <outputDirectory>${project.build.directory}</outputDirectory>
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
            <!--docker的maven插件，详情请见 https://blog.csdn.net/SIMBA1949/article/details/83064083-->
        </plugins>
    </build>

    <profiles>
        <profile>
            <!-- clean compile package -P 'pom中环境对应的id值' -->
            <!-- id唯一 -->
            <id>dev</id>
            <properties>
                <!-- 环境标识，需要与配置文件的名称相对应 -->
                <activatedProperties>dev</activatedProperties>
            </properties>
            <activation>
                <!-- 默认环境 -->
                <activeByDefault>true</activeByDefault>
            </activation>
        </profile>
        <profile>
            <id>test</id>
            <properties>
                <activatedProperties>test</activatedProperties>
            </properties>
        </profile>
        <profile>
            <id>prod</id>
            <properties>
                <activatedProperties>prod</activatedProperties>
            </properties>
        </profile>
    </profiles>
</project>
```

### controller 层

```java
package top.simba1949.controller;

import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

import java.time.LocalDateTime;

/**
 * @Author Theodore
 * @Date 2020/3/19 10:43
 */
@RestController
@RequestMapping("test")
public class TestController {

    @GetMapping
    public String sayHello(){
        return "Welcome To My World";
    }

    @GetMapping("now")
    public LocalDateTime getLocalDateTime(){
        return LocalDateTime.now();
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
 * @Date 2020/3/19 10:36
 */
@SpringBootApplication
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
```

### 配置文件

#### application.yml

```yaml
spring:
  profiles:
    active: @activatedProperties@
```

#### application-dev.yml

```yaml
server:
  port: 8080
spring:
  application:
    name: spring-boot-package-project-dev
```

#### application-test.yml

```yaml
server:
  port: 18080
spring:
  application:
    name: spring-boot-package-project-test
```

#### application-prod.yml

```yaml
server:
  port: 28080
spring:
  application:
    name: spring-boot-package-project-prod
```

### 打包命令

#### 开发环境

```shell
clean compile package install -P dev
```

#### 测试环境

```sehll
clean compile package install -P test
```

#### 生成环境

```shell
clean compile package install -P prod
```

