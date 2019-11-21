# ElasticSearch的Docker安装后简易配置

## 前言

## 配置

### 配置 ES

```shell
# 进入 es 的 docker 容器中，当前目录为 /usr/share/elasticsearch
docker exec -it d5d232b4954e /bin/bash
# 进入 config 目录
cd config
vim elasticsearch.yml
```

配置内容如下

```yaml
# 集群名称，默认为 docker-cluster，可自行更改
cluster.name: "docker-cluster"
network.host: 0.0.0.0
transport.host: 0.0.0.0
# 允许跨域
http.cors.enabled: true
# 允许哪些域跨域
http.cors.allow-origin: "*"
```

### 配置系统

 修改 `/etc/security/limits.conf` 文件，增加如下配置

```conf
# End of file
*               soft    nofile          65536
*               hard    nofile          65536
*               soft    nproc           4096
*               hard    nproc           4096
```

修改 ` /etc/sysctl.conf ` 文件，如果没有自行闯进，增加如下配置，执行 ` sysctl -p ` 使其生效

```cong
vm.max_map_count=262144
```

## 重启 Docker 容器

```
# 重启docker
docker restart 容器名称/容器ID
# 容器伴随 docker 启动而启动
docker update --restart=always 容器名称/容器ID
```

## 访问测试

访问 ip:9200 看到如下内容即可

```json
{
  "name" : "454244b9aa0c",
  "cluster_name" : "docker-cluster",
  "cluster_uuid" : "yEngO-hZTJuycdfHQ1I5Tw",
  "version" : {
    "number" : "7.4.2",
    "build_flavor" : "default",
    "build_type" : "docker",
    "build_hash" : "2f90bbf7b93631e52bafb59b3b049cb44ec25e96",
    "build_date" : "2019-10-28T20:40:44.881551Z",
    "build_snapshot" : false,
    "lucene_version" : "8.2.0",
    "minimum_wire_compatibility_version" : "6.8.0",
    "minimum_index_compatibility_version" : "6.0.0-beta1"
  },
  "tagline" : "You Know, for Search"
}
```

