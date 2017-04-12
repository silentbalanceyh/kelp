# Dockerfile写法

## FROM

用法：

* `FROM <image>`
* `FROM <image>:<tag>`
* `FROM <image>@<digest>`

信息：

* **FROM**必须是没有注释的代码行
* **FROM**如果需要创建多个镜像，在单个Dockerfile文件中FROM可以出现多次——在每个FROM之前，简单使用注释备注一下当前这个镜像的ImageID
* 


