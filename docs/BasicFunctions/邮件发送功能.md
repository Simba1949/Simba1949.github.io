# 邮件发送功能

## 前言

版本说明

```properties
jdk=1.8.0_221
maven=3.6.1
# 模板引擎 spring-boot-starter-freemarker
springboot=2.2.0.RELEASE
commons-email=1.5
```

相关链接：

* common-email maven 地址：https://mvnrepository.com/artifact/org.apache.commons/commons-email

## 项目实战

### 核心依赖

```xml
<!-- https://mvnrepository.com/artifact/org.apache.commons/commons-email -->
<dependency>
    <groupId>org.apache.commons</groupId>
    <artifactId>commons-email</artifactId>
    <version>${commons-email.version}</version>
</dependency>
```

### 配置信息

#### pom 依赖

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>top.simba1949</groupId>
    <artifactId>mail-function</artifactId>
    <version>1.0-SNAPSHOT</version>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.2.0.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>

    <!--版本管理-->
    <properties>
        <spring-cloud-context.version>2.1.3.RELEASE</spring-cloud-context.version>
        <fastjson.version>1.2.62</fastjson.version>
        <commons-email.version>1.5</commons-email.version>
    </properties>

    <dependencies>
        <!--spring boot starter : Core starter, including auto-configuration support, logging and YAML-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter</artifactId>
        </dependency>
        <!-- spring-cloud-context，使 bootstrap.properties 配置文件生效 -->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-context</artifactId>
            <version>${spring-cloud-context.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-configuration-processor</artifactId>
            <optional>true</optional>
        </dependency>
        <!--actuator-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
        <!--spring-boot-devtools-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <optional>true</optional> <!-- 表示依赖不会传递 -->
        </dependency>
        <!--lombok-->
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
        </dependency>
        <!-- springboot test -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>

        <!--web support-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <!-- https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-starter-freemarker -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-freemarker</artifactId>
        </dependency>

        <!-- https://mvnrepository.com/artifact/org.apache.commons/commons-lang3 -->
        <dependency>
            <groupId>org.apache.commons</groupId>
            <artifactId>commons-lang3</artifactId>
        </dependency>
        <!-- https://mvnrepository.com/artifact/org.apache.commons/commons-email -->
        <dependency>
            <groupId>org.apache.commons</groupId>
            <artifactId>commons-email</artifactId>
            <version>${commons-email.version}</version>
        </dependency>
    </dependencies>

    <build>
        <defaultGoal>install</defaultGoal>
        <plugins>
            <!--compiler plugin-->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.8.0</version>
                <configuration>
                    <source>1.8</source>
                    <target>1.8</target>
                </configuration>
            </plugin>
            <!--the plugin of resources copy-->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-resources-plugin</artifactId>
                <version>3.1.0</version>
                <configuration>
                    <encoding>UTF-8</encoding>
                </configuration>
            </plugin>
            <!--打包插件-->
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <configuration>
                    <!--main of springboot project-->
                    <!--<mainClass>top.simba1949.Application</mainClass>-->
                    <!-- 如果没有该配置，devtools不会生效 -->
                    <fork>true</fork>
                    <!--将项目注册到linux服务上，可以通过命令开启、关闭以及伴随开机启动等功能-->
                    <executable>true</executable>
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

#### application.yml 配置文件

```yaml
server:
  port: 18080

spring:
  application:
    name: pangu
```

#### mail.properties 邮件核心配置文件

```properties
# stmp 服务器地址
email.server.host=
# 邮箱账号
email.sender.account=
# 邮箱密码
email.sender.password=
# 发送人
email.sender.username=
```

#### VerificationCodeTmp.ftl 模板文件

```ftl
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title></title>
</head>
<body>
<h1>邀请码 ${code}</h1>
</body>
</html>
```

### 邮件类 MailInfo

```java
package top.simba1949.common;

import lombok.Data;
import org.apache.commons.mail.EmailAttachment;

import java.util.List;

/**
 * @Author Theodore
 * @Date 2019/10/23 14:45
 */
@Data
public class MailInfo {
    /** 收件人 */
    private List<String> toAddress;
    /** 抄送人地址 */
    private List<String> ccAddress;
    /** 密送人 */
    private List<String> bccAddress;
    /** 附件信息 */
    private List<EmailAttachment> attachments;
    /** 邮件主题 */
    private String subject;
    /** 邮件的文本内容 */
    private String content;
}
```

### 读取 mail.properties 配置类 MailConfig

```java
package top.simba1949.config;

import lombok.Data;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.PropertySource;

/**
 * @Author Theodore
 * @Date 2019/10/23 14:44
 */
@Data
@Configuration
@PropertySource(value = "classpath:mail.properties", encoding = "utf-8")
public class MailConfig {

    public static String emailServerHost;
    public static String senderAccount;
    public static String senderPassword;
    public static String senderUsername;

    @Autowired
    public void setEmailServerHost(@Value("${email.server.host}")String host) {
        emailServerHost = host;
    }
    @Autowired
    public void setSenderAccount(@Value("${email.sender.account}") String account){
        senderAccount = account;
    }
    @Autowired
    public void setSenderPassword(@Value("${email.sender.password}")String password){
        senderPassword = password;
    }
    @Autowired
    public void setSenderUsername(@Value("${email.sender.username}")String username){
        senderUsername = username;
    }
}
```

### freemarker 配置类 FreemarkerConfig

```java
package top.simba1949.config;

import freemarker.template.Template;
import freemarker.template.TemplateException;
import lombok.Data;
import org.springframework.context.annotation.Configuration;

import java.io.File;
import java.io.IOException;
import java.io.StringWriter;
import java.util.Locale;
import java.util.Map;

/**
 * @Author Theodore
 * @Date 2019/10/23 14:43
 */
@Data
@Configuration
public class FreemarkerConfig {
    /**
     * 初始化 FreeMarker 相关配置
     * @param path
     * @return
     * @throws IOException
     */
    private static freemarker.template.Configuration init(String path) throws IOException {
        freemarker.template.Configuration configuration = new freemarker.template.Configuration(freemarker.template.Configuration.getVersion());
        configuration.setEncoding(Locale.CHINESE, "utf-8");
        configuration.setDirectoryForTemplateLoading(new File(path));
        return configuration;
    }

    /**
     * 获取模板对象
     * @param path
     * @param templateName
     * @return
     * @throws IOException
     */
    public static Template getTemplate(String path, String templateName) throws IOException {
        freemarker.template.Configuration configuration = init(path);
        Template template = configuration.getTemplate(templateName);
        return template;
    }

    /**
     * 将模板转出字符串
     * @param path
     * @param templateName
     * @param dataMap
     * @return
     * @throws IOException
     * @throws TemplateException
     */
    public static String templateToString(String path, String templateName, Map<String, Object> dataMap) throws IOException, TemplateException {
        StringWriter stringWriter = new StringWriter();
        Template template = getTemplate(path, templateName);
        template.process(dataMap, stringWriter);;
        return stringWriter.toString();
    }
}
```

### 发送邮件核心类 EmailUtils

```java
package top.simba1949.util;

import org.apache.commons.mail.EmailAttachment;
import org.apache.commons.mail.EmailException;
import org.apache.commons.mail.HtmlEmail;
import top.simba1949.common.MailInfo;
import top.simba1949.config.MailConfig;

import java.util.List;

/**
 * @Author Theodore
 * @Date 2019/10/23 14:45
 */
public class EmailUtils {
    /**
     * 发送邮件
     * @param mailInfo
     */
    public static void sendEmail(MailInfo mailInfo){

        try {
            HtmlEmail email = new HtmlEmail();
            // mail.properties 配置信息
            email.setHostName(MailConfig.emailServerHost);
            email.setFrom(MailConfig.senderAccount, MailConfig.senderUsername);
            email.setAuthentication(MailConfig.senderAccount, MailConfig.senderPassword);
            email.setCharset("UTF-8");

            // 邮件主题
            email.setSubject(mailInfo.getSubject());
            // 邮件内容
            email.setHtmlMsg(mailInfo.getContent());
            // 收件人
            List<String> toAddress = mailInfo.getToAddress();
            if (null != toAddress && toAddress.size() > 0) {
                for (int i = 0; i < toAddress.size(); i++) {
                    email.addTo(toAddress.get(i));
                }
            }
            // 抄送人
            List<String> ccAddress = mailInfo.getCcAddress();
            if (null != ccAddress && ccAddress.size() > 0) {
                for (int i = 0; i < ccAddress.size(); i++) {
                    email.addCc(ccAddress.get(i));
                }
            }
            // 密送人
            List<String> bccAddress = mailInfo.getBccAddress();
            if (null != bccAddress && bccAddress.size() > 0) {
                for (int i = 0; i < bccAddress.size(); i++) {
                    email.addBcc(ccAddress.get(i));
                }
            }
            // 添加附件
            List<EmailAttachment> attachments = mailInfo.getAttachments();
            if (null != attachments && attachments.size() > 0) {
                for (int i = 0; i < attachments.size(); i++) {
                    email.attach(attachments.get(i));
                }
            }

            // 发送邮件
            email.send();
        } catch (EmailException e) {
            e.printStackTrace();
        }
    }
}
```

### 封装邮件信息类 UserService

```java
package top.simba1949.service;

import freemarker.template.TemplateException;
import org.springframework.stereotype.Service;
import top.simba1949.common.MailInfo;
import top.simba1949.config.FreemarkerConfig;
import top.simba1949.util.EmailUtils;

import java.io.IOException;
import java.util.Arrays;
import java.util.HashMap;
import java.util.Map;
import java.util.UUID;

/**
 * @Author Theodore
 * @Date 2019/10/23 14:41
 */
@Service
public class UserService {

    public void getEmailCode(String email) {
        // 获取邮箱验证码
        String verificationCode = UUID.randomUUID().toString().replaceAll("-", "");

        // 邮件普通html内容
        String subject = "用户密码找回";
        StringBuilder sb = new StringBuilder("亲爱的伙伴，您好！<br/>\n");
        sb.append("您收到这封这封电子邮件是因为您 (也可能是某人冒充您的名义) 提交了找回密码请求<br/>\n");
        sb.append("<b>邀请码：").append(verificationCode).append("</b><br/>\n");
        sb.append("为了保证您帐号的安全性，该验证码有效期为15分钟，并且使用一次后将失效!<br/>\n");
        sb.append("如果这不是您本人提出的资金密码修改申请, 则无需进行任何操作，并可以放心地忽略此电子邮件。<br/>\n");
        sb.append("YUE-SHE PAN-GU");
        String content = sb.toString();

        // 邮件发送html模板内容
        // 模板根目录
        String path = this.getClass().getResource("/").getFile();
        Map dataMap = new HashMap<>(16);
        dataMap.put("code", verificationCode);
        // 生成的模板转换成字符串输出
        String htmlStr = "";
        try {
            htmlStr = FreemarkerConfig.templateToString(path + "/mail-template", "VerificationCodeTmp.ftl", dataMap);
        } catch (IOException e) {
            e.printStackTrace();
        } catch (TemplateException e) {
            e.printStackTrace();
        }

        // 构建邮件信息
        MailInfo mailInfo = new MailInfo();
        mailInfo.setToAddress(Arrays.asList(email));
        mailInfo.setSubject(subject);
        // mailInfo.setContent(content);
        mailInfo.setContent(htmlStr);

        EmailUtils.sendEmail(mailInfo);
    }
}
```

### 触发邮件发送类 UserController

```java
package top.simba1949.controller;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;
import top.simba1949.service.UserService;

/**
 * @Author Theodore
 * @Date 2019/10/23 14:40
 */
@RestController
@RequestMapping("user")
public class UserController {

    @Autowired
    private UserService userService;

    /**
     * 获取邮箱验证码
     * @param email
     * @return
     */
    @GetMapping("get-email-code")
    public String getEmailCode(String email){
        userService.getEmailCode(email);
        return "SUCCESS";
    }
}
```

### 启动类 MailApplication

```java
package top.simba1949;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

/**
 * @Author Theodore
 * @Date 2019/10/23 14:38
 */
@SpringBootApplication
public class MailApplication {
    public static void main(String[] args) {
        SpringApplication.run(MailApplication.class, args);
    }
}
```







