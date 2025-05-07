# 当一个Docker容器运行异常时，如何通过Docker命令查看日志信息

<font style="color:rgb(5, 7, 59);background-color:rgb(253, 253, 254);">当一个Docker容器运行异常时，你可以使用Docker命令查看容器的日志信息。有三种方法可以实现：</font>

1. <font style="color:rgb(5, 7, 59);background-color:rgb(253, 253, 254);">使用</font><font style="color:rgb(5, 7, 59);background-color:rgb(253, 253, 254);">docker logs</font><font style="color:rgb(5, 7, 59);background-color:rgb(253, 253, 254);">命令。该命令可以查看容器的日志输出。例如，要查看名为"my-container"的容器的日志，可以运行以下命令：</font>

```bash
docker logs my-container
```

<font style="color:rgb(5, 7, 59);background-color:rgb(253, 253, 254);">默认情况下，</font><font style="color:rgb(5, 7, 59);background-color:rgb(253, 253, 254);">docker logs</font><font style="color:rgb(5, 7, 59);background-color:rgb(253, 253, 254);">命令将显示容器的全部日志内容。如果你只想查看最新的几行日志，可以使用</font><font style="color:rgb(5, 7, 59);background-color:rgb(253, 253, 254);">-tail</font><font style="color:rgb(5, 7, 59);background-color:rgb(253, 253, 254);">选项指定行数，如：</font>

```bash
docker logs --tail 10 my-container
```

<font style="color:rgb(5, 7, 59);background-color:rgb(253, 253, 254);">这会显示最新的10行日志。</font>

2. <font style="color:rgb(5, 7, 59);background-color:rgb(253, 253, 254);">进入容器内部查看日志。首先使用</font><font style="color:rgb(5, 7, 59);background-color:rgb(253, 253, 254);">docker ps</font><font style="color:rgb(5, 7, 59);background-color:rgb(253, 253, 254);">命令找到容器的ID，然后运行以下命令进入容器的命令行界面：</font>

```bash
docker exec -it [容器ID] /bin/bash
```

<font style="color:rgb(5, 7, 59);background-color:rgb(253, 253, 254);">进入容器后，你可以使用</font><font style="color:rgb(5, 7, 59);background-color:rgb(253, 253, 254);">cat</font><font style="color:rgb(5, 7, 59);background-color:rgb(253, 253, 254);">或</font><font style="color:rgb(5, 7, 59);background-color:rgb(253, 253, 254);">less</font><font style="color:rgb(5, 7, 59);background-color:rgb(253, 253, 254);">等命令查看日志文件。日志文件通常位于</font><font style="color:rgb(5, 7, 59);background-color:rgb(253, 253, 254);">/var/log/</font><font style="color:rgb(5, 7, 59);background-color:rgb(253, 253, 254);">目录下。</font>

3. <font style="color:rgb(5, 7, 59);background-color:rgb(253, 253, 254);">使用</font><font style="color:rgb(5, 7, 59);background-color:rgb(253, 253, 254);">docker attach</font><font style="color:rgb(5, 7, 59);background-color:rgb(253, 253, 254);">命令实时查看容器日志。该命令允许你实时跟踪容器的日志输出。例如，要查看名为"my-container"的容器的日志，可以运行以下命令：</font>

```bash
docker attach my-container
```

<font style="color:rgb(5, 7, 59);background-color:rgb(253, 253, 254);">这会附加到容器的标准输出和标准错误输出，实时显示容器的日志信息。注意，一旦你使用</font><font style="color:rgb(5, 7, 59);background-color:rgb(253, 253, 254);">docker attach</font><font style="color:rgb(5, 7, 59);background-color:rgb(253, 253, 254);">进入容器后，将无法再退出容器。如果要退出，可以按下</font><font style="color:rgb(5, 7, 59);background-color:rgb(253, 253, 254);">Ctrl + C</font><font style="color:rgb(5, 7, 59);background-color:rgb(253, 253, 254);">组合键强制中断容器运行。</font>



> 更新: 2023-09-05 14:23:27  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/lg3thlrkpf2zi6kx>