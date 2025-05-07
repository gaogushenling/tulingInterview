# 如何将一个Docker镜像上传到Docker Hub

<font style="color:rgb(5, 7, 59);background-color:rgb(253, 253, 254);">要将一个Docker镜像上传到Docker Hub，可以按照以下步骤进行操作：</font>

1. <font style="color:rgb(5, 7, 59);background-color:rgb(253, 253, 254);">在Docker Hub中创建一个新的存储库。点击右上角"Create Repository"按钮，给存储库取一个名字，并选择是公有存储库还是私有存储库。</font>
2. <font style="color:rgb(5, 7, 59);background-color:rgb(253, 253, 254);">在本地构建Docker镜像。进入包含Dockerfile的目录，使用以下命令构建镜像：</font>

```bash

docker image build -t [username]/[repository]:[tag] .
```

<font style="color:rgb(5, 7, 59);background-color:rgb(253, 253, 254);">这里的</font><font style="color:rgb(5, 7, 59);background-color:rgb(253, 253, 254);">[username]</font><font style="color:rgb(5, 7, 59);background-color:rgb(253, 253, 254);">是您的Docker Hub用户名，</font><font style="color:rgb(5, 7, 59);background-color:rgb(253, 253, 254);">[repository]</font><font style="color:rgb(5, 7, 59);background-color:rgb(253, 253, 254);">是您在Docker Hub上创建的存储库名称，</font><font style="color:rgb(5, 7, 59);background-color:rgb(253, 253, 254);">[tag]</font><font style="color:rgb(5, 7, 59);background-color:rgb(253, 253, 254);">是镜像的标签。例如：</font>

```bash

docker image build -t fox666/tulingmall-product:0.0.1 .
```

3. <font style="color:rgb(5, 7, 59);background-color:rgb(253, 253, 254);">登录Docker Hub。在本地使用以下命令登录：</font>

```bash


docker login
```

<font style="color:rgb(5, 7, 59);background-color:rgb(253, 253, 254);">输入您的Docker Hub用户名和密码进行登录。</font>

4. <font style="color:rgb(5, 7, 59);background-color:rgb(253, 253, 254);">将本地构建的Docker镜像推送到Docker Hub。使用以下命令：</font>

```bash

docker push [username]/[repository]:[tag]
```

<font style="color:rgb(5, 7, 59);background-color:rgb(253, 253, 254);">这里的</font><font style="color:rgb(5, 7, 59);background-color:rgb(253, 253, 254);">[username]</font><font style="color:rgb(5, 7, 59);background-color:rgb(253, 253, 254);">是您的Docker Hub用户名，</font><font style="color:rgb(5, 7, 59);background-color:rgb(253, 253, 254);">[repository]</font><font style="color:rgb(5, 7, 59);background-color:rgb(253, 253, 254);">是您在Docker Hub上创建的存储库名称，</font><font style="color:rgb(5, 7, 59);background-color:rgb(253, 253, 254);">[tag]</font><font style="color:rgb(5, 7, 59);background-color:rgb(253, 253, 254);">是镜像的标签。例如：</font>

```bash

docker push fox666/tulingmall-product:0.0.1
```

<font style="color:rgb(5, 7, 59);background-color:rgb(253, 253, 254);">上传过程中，Docker将会逐层上传镜像的每个层，并计算每个层的SHA256哈希值。在上传完成后，可以在Docker Hub上看到已上传的镜像。</font>



> 更新: 2023-09-05 14:23:48  
> 原文: <https://www.yuque.com/tulingzhouyu/db22bv/aikcw5e9saiqvo1y>