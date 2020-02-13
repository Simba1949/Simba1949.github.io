# 项目构建

## 前言

## Springboot 项目构建推荐

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>top.simba1949</groupId>
    <artifactId>netty-learn</artifactId>
    <version>1.0-SNAPSHOT</version>

    <properties>
        <finalName.text>project-final-name</finalName.text>
        <!--https://mvnrepository.com/artifact/org.springframework.cloud/spring-cloud-dependencies -->
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
        <version>2.2.4.RELEASE</version>
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
            <!--The Resources Plugin handles the copying of project resources to the output directory. -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-resources-plugin</artifactId>
                <configuration>
                    <encoding>UTF-8</encoding>
                </configuration>
            </plugin>
            <!--Spring Boot Maven Plugin-->
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <configuration>
                    <!--main of springboot project-->
                    <!--<mainClass>top.simba1949.Application</mainClass>-->
                    <!-- 如果没有该配置，devtools不会生效 -->
                    <fork>true</fork>
                    <skip>true</skip>
                    <!--将项目注册到linux服务上，可以通过命令开启、关闭以及伴随开机启动等功能-->
                    <executable>true</executable>
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
            <!-- docker的maven插件，详情请见 https://blog.csdn.net/SIMBA1949/article/details/83064083-->
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
</project>
```

