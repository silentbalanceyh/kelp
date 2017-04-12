# Dockerfile写法

## FROM

用法：

* `FROM <image>`
* `FROM <image>:<tag>`
* `FROM <image>@<digest>`

信息：

* **FROM**必须是没有注释的代码行
* **FROM**如果需要创建多个镜像，在单个Dockerfile文件中FROM可以出现多次——在每个FROM之前，简单使用注释备注一下当前这个镜像的ImageID
* `tag` 和`digest` 两个值是可选的，如果没提供则使用`latest` 的值体改替代，如果builder无法匹配`tag` 中的值则报错

## MAINTAINER

用法：

* `MAINTAINER <name>`

该命令允许您在文件中设置作者信息。

## RUN

用法：

* `RUN <command>` （shell格式，command命令将在shell中运行，Linux中默认使用`/bin/sh -c` ，在Windows中默认使用`cmd /S /C` ）
* `RUN ["<executable>","<param1>","<param2>"]`exec格式

信息：

exec格式可防止shell中的



