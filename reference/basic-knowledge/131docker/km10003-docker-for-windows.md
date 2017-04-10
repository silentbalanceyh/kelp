# Docker for Windows

## 0. Tools

* Docker Machine——它将改变看待Docker的角度：
  * 最传统的方式为Linux Distribution加上Docker，这里的Docker在使用者看来，其实和其他Linux上的软件概念没什么不同，仅是一个应用程序。
  * 而CoreOS将Docker进一步提升，在CoreOS中，Docker是用户要运行其他软件的核心平台，否则别无他法。
  * Docker Machine的出现将Docker地位再次提升，用户创建Docker Machine时就只关心Docker，不关心其他组件。
* Docker Swarm——（A Docker-Native Clustering System），CoreOS主战场是集群环境，可对比Docker Swarm和CoreOS Fleet对比，二者都用于构建集群工具。
* Docker Compose——这个特性中规中矩，是Docker应用场景的自然衍生，就Docker设计准则来说，他的目标是足够轻量、只运行一个应用，做一个应用容器，而不是变成一个OS容器。所以Compose引入了group概念，将写作容器组成一个Group，然后使用YAML文件来定义Group，同时对Docker各个标准API进行扩展以支持group、build、pull、run、start、stop等，还引入了`docker up`

## 1. Versions

安装完成过后，打开命令提示符（`cmd.exe`）或者PowerShell，通过调用`docker`和`docker-compose`来检查版本信息：



