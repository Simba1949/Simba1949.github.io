# 项目构建

## 前言

## Springboot 项目构建推荐

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
<!-- https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-starter-parent -->
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.3.3.RELEASE</version>
    <relativePath></relativePath>
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
        <exclusions>
            <exclusion>
                <groupId>org.junit.vintage</groupId>
                <artifactId>junit-vintage-engine</artifactId>
            </exclusion>
        </exclusions>
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
        <!-- https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-maven-plugin -->
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

## SpringBoot集成MyBatis

```xml
<!-- https://mvnrepository.com/artifact/org.mybatis.spring.boot/mybatis-spring-boot-starter -->
<dependency>
    <groupId>org.mybatis.spring.boot</groupId>
    <artifactId>mybatis-spring-boot-starter</artifactId>
    <version>2.1.3</version>
</dependency>
<!-- https://mvnrepository.com/artifact/tk.mybatis/mapper-spring-boot-starter -->
<dependency>
    <groupId>tk.mybatis</groupId>
    <artifactId>mapper-spring-boot-starter</artifactId>
    <version>2.1.5</version>
</dependency>
<!-- https://mvnrepository.com/artifact/com.github.pagehelper/pagehelper-spring-boot-starter -->
<dependency>
    <groupId>com.github.pagehelper</groupId>
    <artifactId>pagehelper-spring-boot-starter</artifactId>
    <version>1.3.0</version>
</dependency>
<!-- https://mvnrepository.com/artifact/mysql/mysql-connector-java -->
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>8.0.21</version>
</dependency>
<!--oracle 驱动 start -->
<!-- https://mvnrepository.com/artifact/com.oracle.database.jdbc/ojdbc8 -->
<dependency>
    <groupId>com.oracle.database.jdbc</groupId>
    <artifactId>ojdbc8</artifactId>
    <version>19.7.0.0</version>
</dependency>
<!-- https://mvnrepository.com/artifact/com.oracle.database.nls/orai18n -->
<dependency>
    <groupId>com.oracle.database.nls</groupId>
    <artifactId>orai18n</artifactId>
    <version>19.7.0.0</version>
</dependency>
<!--oracle 驱动 end -->
```

## SpringCloud 项目基础依赖

```xml

```

## 工具类基础依赖

### Jackson 依赖

```xml
<!-- https://mvnrepository.com/artifact/com.fasterxml.jackson.core/jackson-databind -->
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-databind</artifactId>
    <version>2.11.2</version>
</dependency>
```

### Apache-Commons

```xml

<!-- https://mvnrepository.com/artifact/org.apache.commons/commons-lang3 -->
<dependency>
    <groupId>org.apache.commons</groupId>
    <artifactId>commons-lang3</artifactId>
    <version>3.11</version>
</dependency>
<!-- https://mvnrepository.com/artifact/org.apache.commons/commons-collections4 -->
<dependency>
    <groupId>org.apache.commons</groupId>
    <artifactId>commons-collections4</artifactId>
    <version>4.4</version>
</dependency>
<!-- https://mvnrepository.com/artifact/commons-validator/commons-validator -->
<dependency>
    <groupId>commons-validator</groupId>
    <artifactId>commons-validator</artifactId>
    <version>1.7</version>
</dependency>
```

### hibernate-validator 依赖

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

### MapStruct pom 依赖

```xml
<dependencies>
    <!-- https://mvnrepository.com/artifact/org.mapstruct/mapstruct -->
    <dependency>
        <groupId>org.mapstruct</groupId>
        <artifactId>mapstruct</artifactId>
        <version>1.3.1.Final</version>
    </dependency>
    <dependency>
        <groupId>org.projectlombok</groupId>
        <artifactId>lombok</artifactId>
        <version>1.18.12</version>
    </dependency>
</dependencies>

<build>
    <plugins>
        <!--https://mvnrepository.com/artifact/org.apache.maven.plugins/maven-compiler-plugin -->
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-compiler-plugin</artifactId>
            <version>3.8.1</version>
            <configuration>
                <source>1.8</source>
                <target>1.8</target>
                <encoding>UTF-8</encoding>
                <annotationProcessorPaths>
                    <!--lombok 和 MapStruct 一起使用需要配置-->
                    <path>
                        <groupId>org.projectlombok</groupId>
                        <artifactId>lombok</artifactId>
                        <version>1.18.12</version>
                    </path>
                    <path>
                        <groupId>org.mapstruct</groupId>
                        <artifactId>mapstruct-processor</artifactId>
                        <version>1.3.1.Final</version>
                    </path>
                </annotationProcessorPaths>
            </configuration>
        </plugin>
    </plugins>
</build>
```

### 可选依赖

```xml
<!-- https://mvnrepository.com/artifact/cn.hutool/hutool-all -->
<dependency>
    <groupId>cn.hutool</groupId>
    <artifactId>hutool-all</artifactId>
    <version>5.4.2</version>
</dependency>

<!-- https://mvnrepository.com/artifact/com.google.guava/guava -->
<dependency>
    <groupId>com.google.guava</groupId>
    <artifactId>guava</artifactId>
    <version>29.0-jre</version>
</dependency>

<!-- https://mvnrepository.com/artifact/com.alibaba/fastjson -->
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>fastjson</artifactId>
    <version>1.2.73</version>
</dependency>
```



