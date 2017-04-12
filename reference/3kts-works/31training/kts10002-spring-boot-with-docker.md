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

**如何使用当前的教程**

* 从起跑线开始：参考[https://spring.io/guides/gs/spring-boot-docker/\#scratch](https://spring.io/guides/gs/spring-boot-docker/#scratch)
* 跳过基础：使用下边命令拉取Guide中的代码：
  ```
  git clone https://github.com/spring-guides/gs-spring-boot-docker.git
  ```
* 使用命令：`cd into gs-spring-boot-docker/initial`
* 查看最上边：[构造Spring Boot应用环境](https://spring.io/guides/gs/spring-boot-docker/#initial)

如果完成了，则可以查看`gs-spring-boot-dokcer/complete`目录中的结果。

**使用Maven构建（关于Gradle部分参考开始的Reference）**

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

Spring Boot Maven Plugin提供了很多方便的功能：

* 它收集所有的类路径的`jars` 来构造可运行的单个jar，这样很方便执行以及传递相关服务。
* 它会搜索`public static void main()` 作为jar运行的主函数。
* 它提供了内置的依赖解决器【Dependency Resolver】来处理不同版本的Spring依赖项，您可以覆盖任何您想要的版本，但默认的Spring相关版本由Spring Boot来设置。

**使用IDE构建：**

设置Spring Boot App，您可以创建简单的Spring Boot应用了：

```
src/main/java/hello/Application.java
```

代码内容如下：

```java
package hello;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@SpringBootApplication
@RestController
public class Application {

    @RequestMapping("/")
    public String home() {
        return "Hello Docker World";
    }

    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }

}
```

这个类使用了`@SpringBootApplication` 和`@RestController` ，它表示使用Spring MVC处理该请求。`@RequestMapping` 将路径`/` 映射到方法`home()` 方法中，仅仅发送”Hello Docker World“的响应，`main()` 方法使用了Spring Boot中的`SpringApplication.run()` 方法运行一个应用。

处理完成过后，则可在非`Docker` 容器中构建该程序：

```
mvn package && java -jar target/gs-spring-boot-docker-0.1.0.jar
```

浏览器打开`localhost:8080`则可以看到输出信息。

**集装箱**

Docker的运行需要一个Dockerfile（语法参考：[KM10005 - Dockerfile语法](/reference/basic-knowledge/131docker/km10005-dockerfileyu-fa.md)），它提供了镜像所需要的层。我们例子中的Dockerfile位置如下：

```
src/main/docker/Dockerfile
```

内容如下：

```shell
FROM frolvlad/alpine-oraclejdk8:slim
VOLUME /tmp
ADD gs-spring-boot-docker-0.1.0.jar app.jar
RUN sh -c 'touch /app.jar'
ENV JAVA_OPTS=""
ENTRYPOINT [ "sh", "-c", \ 
    "java $JAVA_OPTS -Djava.security.egd=file:/dev/./urandom -jar /app.jar" ]
```

Dockerfile很简单，但是您需要运行Spring Boot仅仅需要Java和一个编译好的jar，项目JAR是通过`ADD` 命令添加到容器中作为"app.jar"，然后执行`ENTRYPOINT` 。

**Notes部分不翻译：**

_We added a_`VOLUME`_pointing to "/tmp" because that is where a Spring Boot application creates working directories for Tomcat by default. The effect is to create a temporary file on your host under "/var/lib/docker" and link it to the container under "/tmp". This step is optional for the simple app that we wrote here, but can be necessary for other Spring Boot applications if they need to actually write in the filesystem._

_You can use a_`RUN`_command to "touch" the jar file so that it has a file modification time \(Docker creates all container files in an "unmodified" state by default\). This actually isn’t important for the simple app that we wrote, but any static content \(e.g. "index.html"\) would require the file to have a modification time._

_To reduce _[_Tomcat startup time_](https://wiki.apache.org/tomcat/HowTo/FasterStartUp#Entropy_Source)_ we added a system property pointing to "/dev/urandom" as a source of entropy._

_if you are using boot2docker you need to run it **first **before you do anything with the Docker command line or with the build tools \(it runs a daemon process that handles the work for you in a virtual machine\)._



