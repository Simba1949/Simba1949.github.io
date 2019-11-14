# Gradle 下载与安装

## 前言

版本说明

```properties
gradle=6.0
windows=10
linux.centos=7
```

相关链接

* Gradle 官网：https://gradle.org/
* Gradle 官网安装参考：https://gradle.org/install/
* Gradle 官网下载地址： https://gradle.org/releases/
* SDK 官网： https://sdkman.io/

## Windows 安装

下载并解压

右击此电脑——>属性——>高级系统设置——>环境变量——>在系统变量中找到 ***Path*** ——>新建一条指定 gradle 解压目录下的 ***bin*** 路径即可

即

```
C:\Users\base\Desktop\gradle-6.0\bin
```

### 验证

执行 `gradle -v` 进行验证

```cmd
C:\Users\base>gradle -v

Welcome to Gradle 6.0!

Here are the highlights of this release:
 - Substantial improvements in dependency management, including
   - Publishing Gradle Module Metadata in addition to pom.xml
   - Advanced control of transitive versions
   - Support for optional features and dependencies
   - Rules to tweak published metadata
 - Support for Java 13
 - Faster incremental Java and Groovy compilation
 - New Zinc compiler for Scala
 - VS2019 support
 - Support for Gradle Enterprise plugin 3.0

For more details see https://docs.gradle.org/6.0/release-notes.html


------------------------------------------------------------
Gradle 6.0
------------------------------------------------------------

Build time:   2019-11-08 18:12:12 UTC
Revision:     0a5b531749138f2f983f7c888fa7790bfc52d88a

Kotlin:       1.3.50
Groovy:       2.5.8
Ant:          Apache Ant(TM) version 1.10.7 compiled on September 1 2019
JVM:          1.8.0_221 (Oracle Corporation 25.221-b11)
OS:           Windows 10 10.0 amd64
```

## Linux 安装

### ZIP 包方式安装

下载并解压

编辑 `/etc/profile` 文件，在最后一行添加如下内容

```shell
# gradle。/usr/local/gradle/gradle-6.0/bin 是 gradle 解压后的 bin 目录路径
export PATH=$PATH:/usr/local/gradle/gradle-6.0/bin
```

执行 `source /etc/profile` 重新加载 `/etc/profile` 文件

### SDK  方式安装

#### 安装 SDK

简洁说明

1. 下载安装 `curl -s "https://get.sdkman.io" | bash`
2. 上一步输出的命令 `source "/root/.sdkman/bin/sdkman-init.sh"`
3. 验证 `sdk help`

```shell
## 执行 curl -s "https://get.sdkman.io" | bash
curl -s "https://get.sdkman.io" | bash

                                -+syyyyyyys:
                            `/yho:`       -yd.
                         `/yh/`             +m.
                       .oho.                 hy                          .`
                     .sh/`                   :N`                `-/o`  `+dyyo:.
                   .yh:`                     `M-          `-/osysoym  :hs` `-+sys:      hhyssssssssy+
                 .sh:`                       `N:          ms/-``  yy.yh-      -hy.    `.N-````````+N.
               `od/`                         `N-       -/oM-      ddd+`     `sd:     hNNm        -N:
              :do`                           .M.       dMMM-     `ms.      /d+`     `NMMs       `do
            .yy-                             :N`    ```mMMM.      -      -hy.       /MMM:       yh
          `+d+`           `:/oo/`       `-/osyh/ossssssdNMM`           .sh:         yMMN`      /m.
         -dh-           :ymNMMMMy  `-/shmNm-`:N/-.``   `.sN            /N-         `NMMy      .m/
       `oNs`          -hysosmMMMMydmNmds+-.:ohm           :             sd`        :MMM/      yy
      .hN+           /d:    -MMMmhs/-.`   .MMMh   .ss+-                 `yy`       sMMN`     :N.
     :mN/           `N/     `o/-`         :MMMo   +MMMN-         .`      `ds       mMMh      do
    /NN/            `N+....--:/+oooosooo+:sMMM:   hMMMM:        `my       .m+     -MMM+     :N.
   /NMo              -+ooooo+/:-....`...:+hNMN.  `NMMMd`        .MM/       -m:    oMMN.     hs
  -NMd`                                    :mm   -MMMm- .s/     -MMm.       /m-   mMMd     -N.
 `mMM/                                      .-   /MMh. -dMo     -MMMy        od. .MMMs..---yh
 +MMM.                                           sNo`.sNMM+     :MMMM/        sh`+MMMNmNm+++-
 mMMM-                                           /--ohmMMM+     :MMMMm.       `hyymmmdddo
 MMMMh.                  ````                  `-+yy/`yMMM/     :MMMMMy       -sm:.``..-:-.`
 dMMMMmo-.``````..-:/osyhddddho.           `+shdh+.   hMMM:     :MmMMMM/   ./yy/` `:sys+/+sh/
 .dMMMMMMmdddddmmNMMMNNNNNMMMMMs           sNdo-      dMMM-  `-/yd/MMMMm-:sy+.   :hs-      /N`
  `/ymNNNNNNNmmdys+/::----/dMMm:          +m-         mMMM+ohmo/.` sMMMMdo-    .om:       `sh
     `.-----+/.`       `.-+hh/`         `od.          NMMNmds/     `mmy:`     +mMy      `:yy.
           /moyso+//+ossso:.           .yy`          `dy+:`         ..       :MMMN+---/oys:
         /+m:  `.-:::-`               /d+                                    +MMMMMMMNh:`
        +MN/                        -yh.                                     `+hddhy+.
       /MM+                       .sh:
      :NMo                      -sh/
     -NMs                    `/yy:
    .NMy                  `:sh+.
   `mMm`               ./yds-
  `dMMMmyo:-.````.-:oymNy:`
  +NMMMMMMMMMMMMMMMMms:`
    -+shmNMMMNmdy+:`


                                                                 Now attempting installation...


Looking for a previous installation of SDKMAN...
Looking for unzip...
Looking for zip...
Looking for curl...
Looking for sed...
Installing SDKMAN scripts...
Create distribution directories...
Getting available candidates...
Prime the config file...
Download script archive...
######################################################################## 100.0%
Extract script archive...
Install scripts...
Set version to 5.7.4+362 ...
Attempt update of interactive bash profile on regular UNIX...
Added sdkman init snippet to /root/.bashrc
Attempt update of zsh profile...
Updated existing /root/.zshrc



All done!


Please open a new terminal, or run the following in the existing one:

    source "/root/.sdkman/bin/sdkman-init.sh"

Then issue the following command:

    sdk help

Enjoy!!!
## 在执行上一步命令输出的 source "/root/.sdkman/bin/sdkman-init.sh"
source "/root/.sdkman/bin/sdkman-init.sh"
## 验证
sdk help
```

#### 安装 gradle

```shell
sdk install gradle 6.0
```

### 验证

执行 `gradle -v` 尽心验证

```shell
[root@localhost bin]# gradle -v

Welcome to Gradle 6.0!

Here are the highlights of this release:
 - Substantial improvements in dependency management, including
   - Publishing Gradle Module Metadata in addition to pom.xml
   - Advanced control of transitive versions
   - Support for optional features and dependencies
   - Rules to tweak published metadata
 - Support for Java 13
 - Faster incremental Java and Groovy compilation
 - New Zinc compiler for Scala
 - VS2019 support
 - Support for Gradle Enterprise plugin 3.0

For more details see https://docs.gradle.org/6.0/release-notes.html


------------------------------------------------------------
Gradle 6.0
------------------------------------------------------------

Build time:   2019-11-08 18:12:12 UTC
Revision:     0a5b531749138f2f983f7c888fa7790bfc52d88a

Kotlin:       1.3.50
Groovy:       2.5.8
Ant:          Apache Ant(TM) version 1.10.7 compiled on September 1 2019
JVM:          1.8.0_221 (Oracle Corporation 25.221-b11)
OS:           Linux 3.10.0-1062.4.1.el7.x86_64 amd64
```



