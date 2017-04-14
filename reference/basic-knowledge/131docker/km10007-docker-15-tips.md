# KM10007 - Docker的15个Tips

## 0.环境准备

下载镜像`ubuntu:trusty` 然后创建新的测试镜像，Dockerfile内容如下：

```bash
FROM ubuntu:trusty
MAINTAINER yulang@tradeshiftfinance.com
```

然后使用命令构造新镜像：

```
docker build -t ts_demo:latest -f Dockerfile .
```

运行完成后将得到一个新镜像：

```
Sending build context to Docker daemon 2.048 kB
Step 1/2 : FROM ubuntu:trusty
 ---> 7c09e61e9035
Step 2/2 : MAINTAINER yulang@tradeshiftfinance.com
 ---> Using cache
 ---> c6c11f1147e2
Successfully built c6c11f1147e2
```

查看镜像信息如下：

```
ts_demo             latest              c6c11f1147e2        15 hours ago        188 MB
```

## 1. 得到最后执行的容器ID



