# 免费SSL证书申请&自动续期&永久免费

## 前言

环境： CentOS 7、Nginx 1.16.1、Git 1.8.3.1

Let's Encrypt 官网： https://letsencrypt.org

官方 Github 地址： https://github.com/certbot/certbot

Certbot 客户端官网： https://certbot.eff.org

Certbot 生成证书参考链接： https://certbot.eff.org/lets-encrypt/centosrhel7-nginx

Acme Github 地址： https://github.com/Neilpang/acme.sh.git

sslforfree 官网：https://www.sslforfree.com

## 系统环境准备

更新 yum

```
yum update -y
```

Git

```shell
# 安装 Git
yum install -y git
```

Nginx

```shell
# 添加 Nginx 镜像地址
sudo rpm -Uvh http://nginx.org/packages/centos/7/noarch/RPMS/nginx-release-centos-7-0.el7.ngx.noarch.rpm
# 安装 Nginx
yum install -y nginx
# 查看 Nginx 安装地址
whereis nginx
```

## Cerbot 方式生成证书

参考链接： https://certbot.eff.org/lets-encrypt/centosrhel7-nginx 

### Cerbot 下载

下载

```shell
# 创建 cerbot 文件夹，并进入
mkdir -p  /usr/local/ide/cerbot
cd /usr/local/ide/cerbot
# 下载 cerbot 客户端
git clone https://github.com/certbot/certbot.git
```

### Cerbot 配置

进入 cerbot 下载目录

```shell
# ./certbot-auto certonly  -d 域名 --manual --preferred-challenges dns --server https://acme-v02.api.letsencrypt.org/directory
./certbot-auto certonly  -d jarviss.top -d www.jarviss.top --manual --preferred-challenges dns --server https://acme-v02.api.letsencrypt.org/directory
```

* certonly :  表示安装模式， certonly 表示只是下载证书；
*  -d  :  为哪些域名申请证书，可以使用多个，证书和私钥共用；**注意：填写通配符虽然可以https访问，但是浏览器会报不安全**；
*  --manual  :  表示手动安装插件，Certbot 有很多插件，不同的插件都可以申请证书，用户可以根据需要自行选择 ；
*  --preferred-challenges dns  :  使用 DNS 方式校验域名所有权；
*  --server  :  Let's Encrypt ACME v2 版本使用的服务器不同于 v1 版本，需要显示指定。 

执行上命令后，有几个交互

*  输入邮箱地址
*  是否同意向 Acme 服务器注册 => 同意即可
*  是否共享电子邮箱给  the Electronic Frontier Foundation => 同意即可
*  记录ip => 同意即可
*  需要配置 DNS TXT 记录，校验域名所有权，也就是判断证书申请者是否有域名的所有权。 

***交互后会自动生成证书，并且会说明证书位置，接下可以设置续订或者配置Nginx***

最后生成证书如下

```
IMPORTANT NOTES:
 - Congratulations! Your certificate and chain have been saved at:
   /etc/letsencrypt/live/jarviss.top/fullchain.pem
   Your key file has been saved at:
   /etc/letsencrypt/live/jarviss.top/privkey.pem
   Your cert will expire on 2020-01-26. To obtain a new or tweaked
   version of this certificate in the future, simply run certbot-auto
   again. To non-interactively renew *all* of your certificates, run
   "certbot-auto renew"
 - Your account credentials have been saved in your Certbot
   configuration directory at /etc/letsencrypt. You should make a
   secure backup of this folder now. This configuration directory will
   also contain certificates and private keys obtained by Certbot so
   making regular backups of this folder is ideal.
 - If you like Certbot, please consider supporting our work by:

   Donating to ISRG / Let's Encrypt:   https://letsencrypt.org/donate
   Donating to EFF:                    https://eff.org/donate-le
```

### 设置自动续订 

添加定时任务到 crontab 中，

```shell
echo "0 0,12 * * * root python -c 'import random; import time; time.sleep(random.random() * 3600)' && certbot renew" | sudo tee -a /etc/crontab > /dev/null
```

## Acme 方式生成证书

### Acme 下载与安装

第一种方式（这种下载方式都会超时）

```shell
curl https://get.acme.sh | sh
# 或者
wget -O -  https://get.acme.sh | sh
```

第二种编译方式

```shell
# 安装 socat
yum install -y socat
# 下载与安装
git clone https://github.com/Neilpang/acme.sh.git
cd ./acme.sh
./acme.sh --install
# 新打开一个终端，执行，出现命令提示即为成功
acme.sh --help
```

1. 把 acme.sh 安装到当前用户的主目录`$HOME`下的`.acme.sh`文件夹中，即`~/.acme.sh/`，之后所有生成的证书也会放在这个目录下；
2. 自动创建`cronjob`定时任务, 每天 0:00 点自动检测所有的证书，如果快过期了，则会自动更新证书。

### 生成证书

***需要先启动 Nginx***

据 acme.sh 官方文档介绍，其实现了 acme 协议支持的所有验证协议，一般有两种方式验证：http 和 dns 验证。

执行发证书命令

```shell
acme.sh --issue -d jarviss.top -d www.jarviss.top -w /usr/share/nginx/html --log
```

*  `--issue`是 acme.sh 脚本用来颁发证书的指令； 
*  `-d`是`--domain`的简称，其后面须填写已备案的域名； 
*  `-w`是`--webroot`的简称，其后面须填写网站的根目录。 
*  --log ：便于查看日志

执行之后

```
[Tue Oct 29 08:19:43 CST 2019] Your cert is in  /root/.acme.sh/jarviss.top/jarviss.top.cer 
[Tue Oct 29 08:19:43 CST 2019] Your cert key is in  /root/.acme.sh/jarviss.top/jarviss.top.key 
[Tue Oct 29 08:19:43 CST 2019] The intermediate CA cert is in  /root/.acme.sh/jarviss.top/ca.cer 
[Tue Oct 29 08:19:43 CST 2019] And the full chain certs is there:  /root/.acme.sh/jarviss.top/fullchain.cer
```

## sslforfree 官网方式生成证书

一、输入域名，点击 Create Free SSL Certificate；

二、选择 Manually Verify Domain (DNS)后，点击  **Manually Verify Domain** ；

三、配置 DNS TXT 值，需要配置 **_acme-challenge.域名** 和  **_acme-challenge.www.域名** 的记录值；

四、配置好之后可以通过页面上第三条进行验证；

五、验证成功后，点击 **Download SSL Certificate** 会跳转页面，页面会显示证书和私钥以及CA，也可以 **Download All SSL Certificate Files** 按钮进行打包下载；

六、上传证书和私钥，进行 Nginx 的 SSL 配置。

## Nginx 配置 SSL

```conf
server {
    listen       80;
    server_name  www.jarviss.top;
    # http 请求转发到 https 请求上
	return 301 https://$host$request_uri;
}
server {
    listen       443 ssl default_server;
    server_name  www.jarviss.top;

	# 开启 ssl
	ssl on;
	# 配置证书(可以是 pem/cer/crt 的后缀证书)
    ssl_certificate /etc/letsencrypt/live/jarviss.top/fullchain.pem;
    # 配置私钥(可以是 pem/key/crt 的后缀私钥)
    ssl_certificate_key /etc/letsencrypt/live/jarviss.top/privkey.pem;   
	
	root         /usr/share/nginx/html;
	index        index.html index.htm;
}
```





