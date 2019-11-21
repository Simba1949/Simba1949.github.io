# Kibana下载与安装

## 前言

博客书

版本说明

```properties
kibana=7.4.2
jdk=1.8.0_221
linux.centos=7
windows=10
```

相关链接

* Kibana 官方网址：https://www.elastic.co/cn/products/kibana
* Kibana 官方下载地址：https://www.elastic.co/cn/downloads/kibana
* 官方 docker 镜像地址：https://www.docker.elastic.co/

## 下载安装与配置

### Windows

解压即安装

进入解压目录下，修改  `config/kibana.yml` 

```yaml
# 指定端口
server.port: 5601
# 设置本机host
server.host: "192.168.8.52"
# 配置 elasticsearch 地址
elasticsearch.hosts: ["http://192.168.8.50:9200"]
```

启动访问测试

双击 `bin/kibana` 即可，访问 `ip:5601` 

### Linux

#### TAR 包安装

下载并上传 Tar 包，解压即安装

进入解压目录下，修改 `config/kibana.yml`

```yaml
# 指定端口
server.port: 5601
# 设置本机host
server.host: "192.168.8.52"
# 配置 elasticsearch 地址
elasticsearch.hosts: ["http://192.168.8.50:9200"]
```

执行 `./bin/kibana --allow-root &` 命令，访问 `ip:5601` 即可

### Docker 安装

Docker 镜像地址：https://www.docker.elastic.co/

安装参考链接：https://www.elastic.co/guide/en/kibana/current/docker.html

```shell
# 下载
docker pull docker.elastic.co/kibana/kibana:7.4.2
# 运行
docker run -di --name kibana -p 5601:5601 --restart=always docker.elastic.co/kibana/kibana:7.4.2
# 进入 docker 容器，即进入 /usr/share/kibana 目录 
 docker exec -it kibana /bin/bash
 # 编辑 config/kibana.yml 文件，配置 es 的 hosts
 elasticsearch.hosts: [ "http://192.168.8.50:9200" ]
```







