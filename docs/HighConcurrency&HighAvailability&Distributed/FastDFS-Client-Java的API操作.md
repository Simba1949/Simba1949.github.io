# FastDFS-Client-Java的API操作

## 前言

版本说明

```properties
fastdfs=5.12
fastdfs-nginx-module=1.20
libfastcommon=1.0.40
nginx=1.17.4
jdk=1.8.0_211
maven=3.6.1
springboot=2.2.0.RELEASE
```

参考链接

*  fastdfs-client-java github地址： https://github.com/happyfish100/fastdfs-client-java 
*  fastdfs-client-java  maven 地址：https://mvnrepository.com/artifact/net.oschina.zcx7878/fastdfs-client-java 

>  注：org.csource/fastdfs-client-java/1.27-RELEASE 版本maven无法自动导入到项目中，可以使用 net.oschina.zcx7878/fastdfs-client-java 替代，或者通过 github 源码编译后导入到项目中 

## 项目实战

### 核心依赖

```xml
<fastdfs-client-java.version>1.27.0.0</fastdfs-client-java.version>
<!-- https://mvnrepository.com/artifact/net.oschina.zcx7878/fastdfs-client-java -->
<dependency>
    <groupId>net.oschina.zcx7878</groupId>
    <artifactId>fastdfs-client-java</artifactId>
    <version>${fastdfs-client-java.version}</version>
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
    <artifactId>file-upload-function</artifactId>
    <version>1.0-SNAPSHOT</version>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.2.0.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>

    <!--版本管理-->
    <properties>
        <java.version>1.8</java.version>
        <spring-cloud-context.version>2.1.3.RELEASE</spring-cloud-context.version>
        <fastdfs-client-java.version>1.27.0.0</fastdfs-client-java.version>
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
        <!-- https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-configuration-processor -->
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
        
        <!-- https://mvnrepository.com/artifact/net.oschina.zcx7878/fastdfs-client-java -->
        <dependency>
            <groupId>net.oschina.zcx7878</groupId>
            <artifactId>fastdfs-client-java</artifactId>
            <version>${fastdfs-client-java.version}</version>
        </dependency>
    </dependencies>

    <build>
        <defaultGoal>install</defaultGoal>
        <plugins>
            <!--compiler plugin-->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <configuration>
                    <source>${java.version}</source>
                    <target>${java.version}</target>
                </configuration>
            </plugin>
            <!--the plugin of resources copy-->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-resources-plugin</artifactId>
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

#### fastdfs 核心配置

```properties
# fastdfs 核心配置文件
# 连接超时时间
connect_timeout=60
# 网络请求超时时间
network_timeout=60
charset=UTF-8
# tracker 的 http 请求端口
http.tracker_http_port=8080
# tracker 的 tcp 通信IP&PORT
tracker_server=192.168.8.46:22122
# tracker_server=192.168.8.129:22122
http.anti_steal_token = no
http.secret_key = FastDFS1234567890
```

### 文件描述类 FastDFSFile

```java
package top.simba1949.common;

import lombok.Data;

import java.io.Serializable;

/**
 * @Author Theodore
 * @Date 2019/10/23 16:44
 */
@Data
public class FastDFSFile implements Serializable {

    private static final long serialVersionUID = -6643290550500487894L;
    /** 文件名称 */
    private String name;
    /** 文件内容 */
    private byte[] content;
    /** 文件扩展名 */
    private String ext;
    /** 文件 MD5 摘要值 */
    private String md5;
    /** 文件创建作者 */
    private String author;

    public FastDFSFile(String name, byte[] content, String ext, String md5, String author) {
        this.name = name;
        this.content = content;
        this.ext = ext;
        this.md5 = md5;
        this.author = author;
    }

    public FastDFSFile(String name, byte[] content, String ext) {
        this.name = name;
        this.content = content;
        this.ext = ext;
    }

    public FastDFSFile() {
    }
}
```

### FastDFS 核心类 FastDFSUtils

```java
package top.simba1949.util;

import org.csource.common.MyException;
import org.csource.common.NameValuePair;
import org.csource.fastdfs.*;
import org.springframework.core.io.ClassPathResource;
import top.simba1949.common.FastDFSFile;

import java.io.IOException;
import java.net.InetAddress;

/**
 * 文件管理的核心类
 *
 * @Author Theodore
 * @Date 2019/10/23 16:45
 */
public class FastDFSUtils {

    /**
     * 初始化连接 tracker 链接信息
     */
    static {
        try {
            // new ClassPathResource("fdfs_client.conf").getPath(); 获取classPath目录下的文件
            String path = new ClassPathResource("fdfs_client.conf").getPath();
            // 初始化连接 tracker 链接信息
            ClientGlobal.init(path);
        } catch (IOException e) {
            e.printStackTrace();
        } catch (MyException e) {
            e.printStackTrace();
        }
    }

    /**
     * 文件上传
     * @param fastDFSFile
     */
    public static String upload(FastDFSFile fastDFSFile) throws IOException, MyException {
        // new 一个客户端对象 TrackerClient，用于访问 TrackerServer
        TrackerClient trackerClient = new TrackerClient();
        // 通过 TrackerClient 获取连接，得到 TrackerServer 对象
        TrackerServer trackerServer = trackerClient.getConnection();
        // 通过 trackerClient 的链接信息可以获取 StorageServer 的链接信息
        StorageServer storageServer = trackerClient.getStoreStorage(trackerServer);
        // new 一个Storage客户端访问对象 StorageClient，通过 trackerServer、storageServer 对象
        StorageClient storageClient = new StorageClient(trackerServer, storageServer);

        // 第一个参数是：上传文件的字节数组
        // 第二个参数是：文件的扩展名
        // 第三个参数是；附加信息 NameValuePair[]
        // 假设虚拟附加参数
        NameValuePair[] list = new NameValuePair[1];
        NameValuePair nameValuePair = new NameValuePair("address", "深圳");
        list[0] =  nameValuePair;

        // 通过 StorageClient 访问 storage ，实现文件上传，并且获取文件上传后的存储信息
        String[] rsp = storageClient.upload_file(fastDFSFile.getContent(), fastDFSFile.getExt(), list);
        // 文件服务器ip和端口，建议配置到配置文件中
        String ipPort = "http://192.168.8.46:80";
        // rsp[0] 分组信息
        // rsp[1] 分组中的具体存储地址
        String url = rsp[0] + "/" + rsp[1];

        return ipPort + "/" + url;
    }

    /**
     * 获取文件信息
     * @param groupName
     * @param remoteFileName
     * @return
     * @throws IOException
     * @throws MyException
     */
    public static FileInfo getFileInfo(String groupName, String remoteFileName) throws IOException, MyException {
        // new 一个客户端对象 TrackerClient，用于访问 TrackerServer
        TrackerClient trackerClient = new TrackerClient();
        // 通过 TrackerClient 获取连接，得到 TrackerServer 对象
        TrackerServer trackerServer = trackerClient.getConnection();
        // 通过 trackerClient 的链接信息可以获取 StorageServer 的链接信息
        StorageServer storageServer = trackerClient.getStoreStorage(trackerServer);
        // new 一个Storage客户端访问对象 StorageClient，通过 trackerServer、storageServer 对象
        StorageClient storageClient = new StorageClient(trackerServer, storageServer);

        FileInfo fileInfo = storageClient.get_file_info(groupName, remoteFileName);

        return fileInfo;
    }

    /**
     * 下载文件
     * @param groupName
     * @param remoteFileName
     * @return
     * @throws IOException
     * @throws MyException
     */
    public static byte[] download(String groupName, String remoteFileName) throws IOException, MyException {
        //
        TrackerClient trackerClient = new TrackerClient();
        //
        TrackerServer trackerServer = trackerClient.getConnection();
        //
        StorageServer storageServer = trackerClient.getStoreStorage(trackerServer);
        //
        StorageClient storageClient = new StorageClient(trackerServer, storageServer);

        byte[] bytes = storageClient.download_file(groupName, remoteFileName);

        return bytes;
    }

    /**
     * 删除文件
     * @param groupName
     * @param remoteFileName
     * @return
     * @throws IOException
     * @throws MyException
     */
    public static int delete(String groupName, String remoteFileName) throws IOException, MyException {
        //
        TrackerClient trackerClient = new TrackerClient();
        //
        TrackerServer trackerServer = trackerClient.getConnection();
        //
        StorageServer storageServer = trackerClient.getStoreStorage(trackerServer);
        //
        StorageClient storageClient = new StorageClient(trackerServer, storageServer);

        int result = storageClient.delete_file(groupName, remoteFileName);
        return result;
    }

    /**
     * 获取 storage 信息
     * @return
     * @throws IOException
     */
    public static String getStorage() throws IOException {
        //
        TrackerClient trackerClient = new TrackerClient();
        //
        TrackerServer trackerServer = trackerClient.getConnection();
        //
        StorageServer storageServer = trackerClient.getStoreStorage(trackerServer);

        // 获取 storage
        int storePathIndex = storageServer.getStorePathIndex();
        InetAddress address = storageServer.getInetSocketAddress().getAddress();
        String hostName = storageServer.getInetSocketAddress().getHostName();
        String hostString = storageServer.getInetSocketAddress().getHostString();
        int port = storageServer.getInetSocketAddress().getPort();
        StringBuilder sb = new StringBuilder();
        sb.append("storePathIndex is ").append(storePathIndex).append("; ")
                .append("address is ").append(address).append("; ")
                .append("hostName is ").append(hostName).append("; ")
                .append("hostString is ").append(hostString).append("; ")
                .append("port is ").append(port).append("; ");

        return sb.toString();
    }

    /**
     * 获取 storage 组的 ip 和端口
     * @param groupName
     * @param remoteFileName
     * @return
     * @throws IOException
     */
    public static ServerInfo[] getStorageGroupInfo(String groupName, String remoteFileName) throws IOException {
        //
        TrackerClient trackerClient = new TrackerClient();
        //
        TrackerServer trackerServer = trackerClient.getConnection();

        ServerInfo[] fetchStorage = trackerClient.getFetchStorages(trackerServer, groupName, remoteFileName);
        return fetchStorage;
    }

    /**
     * 获取 tracker 地址
     * @return
     * @throws IOException
     */
    public static String getTrackerInfo() throws IOException {
        //
        TrackerClient trackerClient = new TrackerClient();
        //
        TrackerServer trackerServer = trackerClient.getConnection();

        String hostString = trackerServer.getInetSocketAddress().getHostString();
        return hostString;
    }
}
```

### 文件管理触发类 FileController

```java
package top.simba1949.controller;


import org.csource.common.MyException;
import org.csource.fastdfs.FileInfo;
import org.csource.fastdfs.ServerInfo;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.multipart.MultipartFile;
import top.simba1949.common.FastDFSFile;
import top.simba1949.util.FastDFSUtils;

import javax.servlet.ServletOutputStream;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

/**
 * @Author Theodore
 * @Date 2019/10/23 16:43
 */
@RestController
@RequestMapping("file")
public class FileController {

    @PostMapping("upload")
    public String upload(MultipartFile file) throws IOException, MyException {
        String originalFilename = file.getOriginalFilename();
        String[] split = originalFilename.split("\\.");

        byte[] content = file.getBytes();
        FastDFSFile dfsFile = new FastDFSFile();
        // 文件内容
        dfsFile.setContent(content);
        if (split.length > 1){
            // 原始文件名
            dfsFile.setName(originalFilename.substring(0, originalFilename.length() - split[split.length-1].length() - 1));
            // 原始扩展名
            dfsFile.setExt(split[split.length-1]);
        }else{
            // 如果没有文件扩展名，直接设置名称即可
            dfsFile.setName(split[0]);
        }

        // 文件上传到 FastDFS
        String url = FastDFSUtils.upload(dfsFile);

        return url;
    }

    /**
     * http://192.168.8.46:80/group1/M00/00/00/wKgILl2wFjKAdc4cADPjrn6gMSM998.jpg
     * @param groupName 指的是 group1
     * @param remoteFileName 指的是 M00/00/00/wKgILl2wFjKAdc4cADPjrn6gMSM998.jpg
     * @return
     * @throws IOException
     * @throws MyException
     */
    @GetMapping("file-info")
    public FileInfo getFileInfo(String groupName, String remoteFileName) throws IOException, MyException {
        FileInfo fileInfo = FastDFSUtils.getFileInfo(groupName, remoteFileName);
        return fileInfo;
    }

    @GetMapping("download")
    public void download(String groupName, String remoteFileName, HttpServletResponse rsp) throws IOException, MyException {
        byte[] bytes = FastDFSUtils.download(groupName, remoteFileName);

        // 设置
        String fileName = "下载文件名.jpg";
        fileName = new String(fileName.getBytes("utf-8"), "ISO-8859-1");
        rsp.addHeader("Content-Type", "application/octet-stream");
        rsp.addHeader("Content-Disposition", "attachment; filename=" + fileName);

        ServletOutputStream os = rsp.getOutputStream();
        os.write(bytes);
        os.flush();
    }

    @GetMapping("delete")
    public String delete(String groupName, String remoteFileName) throws IOException, MyException {
        int delete = FastDFSUtils.delete(groupName, remoteFileName);
        return String.valueOf(delete);
    }

    @GetMapping("storage")
    public String getStorage() throws IOException {
        String storage = FastDFSUtils.getStorage();
        return storage;
    }

    @GetMapping("get-storage-group-info")
    public ServerInfo[] getStorageGroupInfo(String groupName, String remoteFileName) throws IOException {
        ServerInfo[] storageGroupInfo = FastDFSUtils.getStorageGroupInfo(groupName, remoteFileName);
        return storageGroupInfo;
    }

    @GetMapping("get-tracker-info")
    public String getTrackerInfo() throws IOException {
        String trackerInfo = FastDFSUtils.getTrackerInfo();
        return trackerInfo;
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
 * @Date 2019/10/23 16:41
 */
@SpringBootApplication
public class FileApplication {
    public static void main(String[] args) {
        SpringApplication.run(FileApplication.class, args);
    }
}
```

