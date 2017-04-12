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

* exec格式可防止shell中使用字符串，直接运行`RUN`命令会使用不包含特殊Shell可执行的基础镜像。
* 默认的shell可直接改成`SHELL`命令来完成。
* 如果直接使用`exec` 格式会触发普通的shell流程，如：

  ```
  RUN ["echo","$HOME"]
  ```

  将会打印环境变量

## CMD

用法：

* `CMD ["<executable>","<param1>","<param2>"]`（直接执行exec方式）
* `CMD ["<param1>","<param2>"]`（默认参数同`ENTRYPOINT`）
* `CMD <command> <param1> <param2>`（Shell模式）

信息：

* `CMD` 的主要意图是提供默认的执行容器，默认容器包含了一个执行器，若你使用了`ENTRYPOINT` 则可省略该执行器。
* 在一个Dockerfile中只能有一个`CMD`指令，若您提供了超过一个`CMD`，则只有最后一个`CMD`会生效。
* 如果`CMD`用来提供了`ENTRYPOINT`指令的默认参数，`CMD`和`ENTRYPOINT`指令应该使用JSON Array的格式。
* 如果为`docker run`制定了特殊参数，它将覆盖默认指定的`CMD`中的参数。
* 如果直接使用`exec`格式会触发普通的shell流程。

## LABEL

用法：

* `LABEL <key>=<value> [<key>=<value> ...]`

信息：

* `LABEL`指令可以在镜像中添加元数据。
* 如果要在LABEL中使用空白，则用引号或反斜杠，就像在命令行解析中一样。
* Labels本身是附加的，包括`FROM`镜像中的`LABEL`。
* 如果Docker遇到了已经存在键的label，新值将会替换掉旧值。
* 若要查看镜像中的labels，则使用`docker inspect`命令，所有信息将位于`"Labels"`的JSON属性中。

## EXPOSE

用法：

* `EXPOSE <port> [<port> ...]`

信息：

* 通知Docker容器运行时将要监听的网络端口。
* `EXPOSE`开放的端口不使用主机端口也可访问。

## ENV

用法：

* `ENV <key> <value>`
* `ENV <key>=<value> [<key>=<value> ...]`

信息：

* 使用`ENV`命令可设置`<key>=<value>`的环境变量。
* 这个值将在所有"descendant"的Dockerfile环境中生效，也可以内联替换。
* 环境变量在容器从镜像运行开始后一直有效。
* 第一个格式可设置但个环境变量，`<value>`中所有字符d吧生效包括空白以及引号。

## ADD

用法：

* `ADD <src> [<src> ...] <dest>`
* `ADD ["<src>",... "<dest>"]`（这种格式用于解析包含了空白字符参数的情况）

信息：

* 拷贝`<src>`指定的新文件、目录或者远程URLs，并且将它添加到镜像中`<dest>`指定的文件系统里。
* `<src>`中可以包含通配符，使用Go语言中的`filepath.Match`规则匹配。
* 如果`<src>`是一个文件或目录，则它们必须是和目录源相关（build的上下文环境）。
* `<dest>`是一个绝对路径，或相当于`WORKDIR`的相对路径。
* 如果`<dest>`不存在，则创建丢失的目录路径。

## COPY

用法：

* `COPY <src> [<src> ...] <dest>`
* `COPY ["<src>", ... "<dest"]`（这种格式用于解析包含了空白字符参数的情况）



