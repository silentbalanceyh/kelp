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

```
C:\Users\Lang>docker --version
Docker version 17.03.1-ce, build c6d412e

C:\Users\Lang>docker-compose --version
docker-compose version 1.11.2, build f963d76f

C:\Users\Lang>docker-machine --version
docker-machine version 0.10.0, build 76ed2a6
```

## 2. Explore the application & Run examples

确认您安装了最新的docker，且命令可用，使用`docker ps` 、`docker version` 、`docker info`命令查看Docker信息：

```
PS C:\Users\Lang> docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             \n
        STATUS              PORTS               NAMES

PS C:\Users\Lang> docker version
Client:
 Version:      17.03.1-ce
 API version:  1.27
 Go version:   go1.7.5
 Git commit:   c6d412e
 Built:        Tue Mar 28 00:40:02 2017
 OS/Arch:      windows/amd64

Server:
 Version:      17.03.1-ce
 API version:  1.27 (minimum version 1.24)
 Go version:   go1.7.5
 Git commit:   c6d412e
 Built:        Tue Mar 28 00:40:02 2017
 OS/Arch:      windows/amd64
 Experimental: true

PS C:\Users\Lang> docker info
Containers: 0
 Running: 0
 Paused: 0
 Stopped: 0
Images: 0
Server Version: 17.03.1-ce
Storage Driver: windowsfilter
 Windows:
Logging Driver: json-file
Plugins:
 Volume: local
 Network: l2bridge l2tunnel nat null overlay transparent
Swarm: inactive
Default Isolation: hyperv
Kernel Version: 10.0 16170 (16170.1000.amd64fre.rs_prerelease.170331-1532)
Operating System: Windows 10 Enterprise
OSType: windows
Architecture: x86_64
CPUs: 8
Total Memory: 15.95 GiB
Name: LANGW520
ID: T2ER:WVIS:7BHW:CYH2:5IY6:4FN4:QPGV:PHQA:JSM6:HM6Q:P7X3:P4PQ
Docker Root Dir: C:\ProgramData\Docker
Debug Mode (client): false
Debug Mode (server): true
 File Descriptors: -1
 Goroutines: 17
 System Time: 2017-04-10T12:43:14.0966205+08:00
 EventsListeners: 0
Registry: https://index.docker.io/v1/
Experimental: true
Insecure Registries:
 127.0.0.0/8
Live Restore Enabled: false
```



