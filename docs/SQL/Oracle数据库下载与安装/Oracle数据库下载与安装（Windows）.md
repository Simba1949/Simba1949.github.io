# Oracle数据库下载与安装（Windows）

## 前言

版本说明

```properties
oracle=19.3
windows=10
linux.centos=8
```

相关链接：

* Oracle 官网：https://www.oracle.com/
* Oracle 数据库地址： https://www.oracle.com/database/technologies/
* Oracle 官方安装教程：https://apexapps.oracle.com/pls/apex/f?p=44785:52:15605902940550:SET_SESSION_SCREEN_READER_ON

## 安装

### 解压并安装

将下载的 Oracle ZIP 安装包进行解压，双击 setup ，开始进行安装

![1](img/1.png)

### 安装时的配置

选择创建并配置单实例数据库

![3](img/3.png)

选择桌面类或者服务器类（示例选择桌面类）

![4](img/4.png)

选择账户配置（示例选择虚拟账户）

![5](img/5.png)

选择 oracle 安装目录和数据目录，并配置全局数据库名称（示例：orcl）和口令

![6](img/6.png)

点击安装（进行安装需要等待很长一段时间）

![7](img/7.png)

![8](img/8.png)

安装完成

![9](img/9.png)

### 访问测试

注意：必须访问 https://localhost:5500/em 才能访问成功，HTTPS 协议，Username 是 `sys` ，密码是安装过程中设置的口令；Container Name 不用填写；

![10](img/11.png)

登录进去

![12](img/12.png)

### 使用 DataGrip 连接

打开 `Net Configuration Assistant` ，选择 `监听程序配置` 

![14](img/14.png)

下一步选择删除，点击下一步退回第一步，选择添加，一直默认设置下一步即可，最后重启 Oracle 服务

![15](img/15.png)

解决 `应当以 SYSDBA 身份或 SYSOPER 身份建立 SYS 连接` 问题

问题如下

![16](img/16.png)

解决方案

将 Advanced 中 `internal_logon` 的值设置为 `sysdba`

![17](img/17.png)

连接测试

![18](img/18.png)



