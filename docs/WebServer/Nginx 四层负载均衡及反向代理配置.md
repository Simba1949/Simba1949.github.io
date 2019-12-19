# Nginx 四层负载均衡及反向代理配置

## 前言

博客书

版本说明

```properties
linux.centos=7
mysql=8.0.18
nginx=1.16.1
```

相关链接

* nginx 官网：http://nginx.org/
* nginx 官网 tcp/udp 配置：http://nginx.org/en/docs/stream/stream_processing.html

## 四层负载均衡及反向代理配置

**四层负载均衡，在网络模型中的传输层中**，基于主要是基于tcp协议报文实现负载均衡（比如LVS、haproxy就是四层负载均衡器），使用改写报文的源地址和目的地址。

nginx 1.9 之后也可以通过配置实现四层负载均衡。四层负载均衡支持所有的负载均衡功能比如数据库，web 服务器等。

### 四层负载均衡代理MySQL数据库

#### 主要配置

```conf
stream {
	# 配置代理
    upstream multi.mysql {
        server 192.168.8.4:3306;
		server 192.168.8.58:3306;
    }
    server {
    	# 监听端口
		listen 13306;
		proxy_connect_timeout 10s;
		proxy_timeout 300s;
		# 配置上游代理服务器
		proxy_pass multi.mysql;
    }
}
```

#### 简易全配置

```conf

user  nginx;
worker_processes  1;

error_log  /var/log/nginx/error.log warn;
pid        /var/run/nginx.pid;

events {
    worker_connections  1024;
}

http {
    include       /etc/nginx/mime.types;
    default_type  application/octet-stream;
    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  /var/log/nginx/access.log  main;
    sendfile        on;
    #tcp_nopush     on;
    keepalive_timeout  65;
    #gzip  on;
    include /etc/nginx/conf.d/*.conf;
}

stream {
    upstream multi.mysql {
        server 192.168.8.4:3306;
		server 192.168.8.58:3306;
    }
    server {
		listen 13306;
		proxy_connect_timeout 10s;
		proxy_timeout 300s;
		proxy_pass multi.mysql;
    }
}
```

