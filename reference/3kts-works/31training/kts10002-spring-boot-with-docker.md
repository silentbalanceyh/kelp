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

* 从起跑线开始：参考[https://spring.io/guides/gs/spring-boot-docker/\#scratch](https://spring.io/guides/gs/spring-boot-docker/#scratch)
* 跳过基础：使用下边命令拉取Guide中的代码：
  ```
  git clone https://github.com/spring-guides/gs-spring-boot-docker.git
  ```
* 使用命令：`cd into gs-spring-boot-docker/initial`
* 查看最上边：[构造Spring Boot应用环境](https://spring.io/guides/gs/spring-boot-docker/#initial)

如果完成了，则可以查看`gs-spring-boot-dokcer/complete`目录中的结果。

**使用Maven构建**

首先您需要设置构建脚本，Maven的构建脚本在这个章节说明。先创建目录结构：在上边创建的目录结构中，使用下边命令创建子目录：

```
mkdir -p src/main/java/hello
```

参考下边pom.xml片段：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" 
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 
    http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>org.springframework</groupId>
    <artifactId>gs-spring-boot-docker</artifactId>
    <version>0.1.0</version>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>1.5.2.RELEASE</version>
    </parent>

    <properties>
        <java.version>1.8</java.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>


    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>

</project>
```



