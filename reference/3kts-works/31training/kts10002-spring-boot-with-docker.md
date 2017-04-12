# KTS10101 - Spring Boot with Docker

[https://spring.io/guides/gs/spring-boot-docker/](https://spring.io/guides/gs/spring-boot-docker/)

该文章教您如何构建一个Docker镜像来运行Spring Boot应用，镜像基本信息：

```
镜像信息：ubuntu:trusty        // Guide中本身使用了alpine-oraclejdk8:slim镜像
代码结构：
code-parent
|-- code-api                 // Restful Api，Spring Boot启动程序
|-- code-lib                 // Java库：Service、Dao、Utility、Model层
构建工具：Maven
开发工具：IntelliJ IDEA
```

[Docker](https://docker.com/)是一个“社交化”的Linux容器管理工具，它允许用户发布镜像到社区并且从上边下载使用镜像信息。

**您需要**

* 15分钟
* 一个IDE文本编辑器
* JDK 1.8+
* Gradle 2.3+或Maven 3.0+
* 开发IDE：
  * Spring Tool Suite \( STS \)
  * IntelliJ IDEA

如果您不是使用的Linux机器，则需要创建一个虚拟机、安装VirtualBox、或者直接使用Mac上的boot2docker，用来管理您的镜像。同样您需要使用Docker——它仅仅只能运行在64bit机器上。参考：[https://docs.docker.com/installation/\#installatio](https://docs.docker.com/installation/#installation)获取安装细节，安装完成过后保证`docker`命令可直接从shell中使用。

**如何使用当前的Guide**



