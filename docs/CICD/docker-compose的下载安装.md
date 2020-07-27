# docker-compose的下载安装

## 安装

前置依赖

```shell
yum -y install epel-release
```

安装 pip3，python3 自带 pip3

```shell
dnf install -y python3
```

配置 pip3 的国内 pip 源，编辑 `~/root~/.pip/pip.conf` 文件（如果不能存在自行创建），配置内容如下：

```
[global]
index-url = https://pypi.tuna.tsinghua.edu.cn/simple
[install]
trusted-host=mirrors.aliyun.com
```

安装

```shell
pip3 install docker-compose
```

## 附录

国内的pip源，如下：

* 阿里云 http://mirrors.aliyun.com/pypi/simple/
* 中国科技大学 https://pypi.mirrors.ustc.edu.cn/simple/
* 豆瓣(douban) http://pypi.douban.com/simple/
* 清华大学 https://pypi.tuna.tsinghua.edu.cn/simple/
* 中国科学技术大学 http://pypi.mirrors.ustc.edu.cn/simple/