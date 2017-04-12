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



