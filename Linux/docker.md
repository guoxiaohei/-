# Docker

## Docker安装

### 卸载旧版本docker

```shell
$ sudo yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine
```

### 设置远程镜像仓库

```shell
$ sudo yum install -y yum-utils

$ sudo yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo
```

## Docker常用命令

### docker命令图

![cmd_logic](https://weiguo-1303915920.cos.ap-nanjing.myqcloud.com/cmd_logic.png)

### 常用命令注释

```shell
attach    Attach to a running container          #当前shell下attach连接指定容器

build     Build an image from a Dockerfile       #通过dockerfile定制镜像

commit    Create a new image from a containers changes   #提交当前容器为新的镜像

cp        Copy files/folders from the containers filesystem to the host path

#从容器中拷贝指定文件或者目录到宿主机中

create    Create a new container                #创建一个新的容器，同run，但不启动容器

diff      Inspect changes on a containers filesystem   #查看docker容器变化

events    Get real time events from the server         #从docker服务获取容器实时事件

exec      Run a command in an existing container       #在已存在的容器上运行命令

export    Stream the contents of a container as a tar archive
#导出容器的内容流作为一个tar归档文件 [ 对应import ]

history   Show the history of an image          #展示一个镜像的历史

images    List images						 	#列出系统当前所有镜像

import    Create a new filesystem image from the contents of a tarball
#从tar包中的内容创建一个新的文件系统映像

info      Display system-wide information      #显示docker相关信息

inspect   Return low-level information on a container  #查看容器详细信息

kill      Kill a running container             #kill指定docker容器

load      Load an image from a tar archive	   #从一个tar包中加载一个镜像

login     Register or Login to the docker registry server  #注册或者登陆一个docker源服务器

logout    Log out from a Docker registry server  #从当前docker registry退出

logs      Fetch the logs of a container         #输出当前容器日志信息

port      Lookup the public-facing port which is NAT-ed to PRIVATE_PORT
#查看映射端口对应的容器内部源端口

pause     Pause all processes within a container #暂停容器

ps        List containers                        #列出容器列表

pull      Pull an image or a repository from the docker registry server
#从docker镜像源服务器拉取指定镜像或者库镜像

push      Push an image or a repository to the docker registry server
#推送指定镜像或者库镜像至docker源服务器

restart   Restart a running container     #重启运行的容器

rm        Remove one or more containers   #移除一个或多个容器

rmi       Remove one or more images		  #移除一个或多个镜像

run       Run a command in a new container  #创建一个新的容器并运行一个命令

save      Save an image to a tar archive	#保存一个镜像为一个tar包（对应load）

search    Search for an image in the docker index  #在docker hub中搜索镜像

start     Start a stopped container			#启动容器

stop      Stop a running container          #停止容器

tag       Tag an image into a repository	#给源中镜像打标签

top       Lookup the running processes of a container  #查看容器中运行的进程信息

unpause   Unpause a paused container		#取消暂停容器

version   Show the docker version information       #查看docker版本号

wait      Block until a container stops, then print its exit code  #截取容器停止时的退出状态值

```

### 实例练习

> Docker 安装Nginx

```shell
#1.搜索镜像
$ docker search nginx 或在https://hub.docker.com/中搜索
#2.下载镜像
$ docker pull nginx
#3.启动容器并命名并映射端口号
$ docker run -d --name nginx01 -p 3344:80 nginx
# -d后台启动
# --name命名
# -p映射端口，宿主机端口：容器内端口
[root@VM-12-7-centos ~]# docker ps 
CONTAINER ID   IMAGE     COMMAND                  CREATED         STATUS         PORTS                  NAMES
17c08dac900d   nginx     "/docker-entrypoint.…"   9 seconds ago   Up 7 seconds   0.0.0.0:3344->80/tcp   nginx01
#4.进入容器
[root@VM-12-7-centos ~]# docker exec -it nginx01 /bin/bash
root@17c08dac900d:/# whereis nginx
nginx: /usr/sbin/nginx /usr/lib/nginx /etc/nginx /usr/share/nginx


```

> Docker安装tomcat

```shell
#官方的使用
docker run -it --rm tomcat:9.0
#不加--rm的效果为停止了容器之后，容器还可以查到。docker run -it --rm，一般用来测试，用完关闭容器即自动删除。(只删除容器镜像还在)
#正常启动运行
docker run -d --name tomcat01 -p 3355:8080 tomcat
#测试访问
#进入容器
docker exec -it tomcat01 /bin/bash
#复制webapps.dist下文件到webapps文件夹
cp -r webapps.dist/* webapps
#查看docker容器资源占用情况 
docker stats
```

### 可视化

> portainer

**什么是portainer?**

Docker图形化界面管理工具！提供一个后台面板供我们操作。

```shell
docker run -d -p 8088:9000 \--restart=always -v /var/run/docker.sock:/var/run/docker.sock --privileged=true portainer/portainer

```

测试访问：1.116.84.179:8088

![image-20210417154542249](https://weiguo-1303915920.cos.ap-nanjing.myqcloud.com/image-20210417154542249.png)

![image-20210417155427190](https://weiguo-1303915920.cos.ap-nanjing.myqcloud.com/image-20210417155427190.png)

## Docker镜像讲解

### 镜像是什么

镜像是一种轻量级，可执行的独立软件包，用来打包软件运行环境和基于运行环境开发的软件，它包含运行某个软件所需的所有内容，包括运行时的代码、库、资源、环境变量和配置文件。

所有的应用，打包成docker镜像，就能直接跑起来。

如何得到镜像：

- 从远处仓库下载
- 从别的地方拷贝
- 自己制作一个镜像DockerFile

### Docker镜像加载原理



> UnionFS（联合文件系统）

UnionFS（联合文件系统）：Union文件系统（UnionFS）是一种分层、轻量级并且高性能的文件系统，他支持对文件系统的修改作为一次提交来一层层的叠加，同时可以将不同目录挂载到同一个虚拟文件系统下（unite several directories into a single virtual filesystem）。Union文件系统是Docker镜像的基础。镜像可以通过分层来进行继承，基于基础镜像（没有父镜像），可以制作各种具体的应用镜像。

特性：一次同时加载多个文件系统，但从外面看起来，只能看到一个文件系统，联合加载会把各层文件叠加起来，这样最终的文件系统会包含所有底层的文件和目录。

> Docker镜像加载原理

docker的镜像实际上由一层一层的文件系统组成，这种层级的文件系统UnionFS。

下面来了解下Linux 操作系统由内核空间和用户空间组成，如下图所示：

![dd5cf83799311a7b50f874205ff9de7a](http://weiguo-1303915920.cos.ap-nanjing.myqcloud.com/f7321fc7bbbfc7ab7e210eee71b9268b.png)

bootfs（boot file system）主要包含bootloader和kernel，bootloader主要是引导加载kernel，Linux刚启动时会加载bootfs文件系统，在Docker镜像的最底层是bootfs。这一层与我们典型的Linux/Unix系统是一样的，包含boot加载器和内核。当boot加载完成之后整个内核就都在内存中了，此时内存的使用权已由bootfs转交给内核，此时系统也会卸载bootfs。

rootfs（root file system），在bootfs之上。包含的就是典型的Linux系统中的/dev，/proc，/bin，/etc等标准目录和文件。rootfs就是各种不同的操作系统发行版，比如Ubuntu，Centos等等。

![18343bbc10cd2568ebafe476337a5e5f](http://weiguo-1303915920.cos.ap-nanjing.myqcloud.com/b97a945a7304ba98162e9af4719397eb.png)

对于一个精简的OS，rootfs可以很小，只需要包含最基本的命令，工具和程序库就可以了，因为底层直接用Host的kernel，自己只需要提供rootfs就可以了。由此可见对于不同的linux发行版，bootfs基本是一致的，rootfs会有差别，因此不同的发行版可以共用bootfs。

