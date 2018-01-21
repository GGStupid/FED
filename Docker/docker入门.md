## 概念

Docker是什么？
Docker是一个虚拟环境容器，可以将你的开发环境、代码、配置文件等一并打包到这个容器中，并发布和应用到任意平台中。比如，你在本地用Python开发网站后台，开发测试完成后，就可以将Python3及其依赖包、Flask及其各种插件、Mysql、Nginx等打包到一个容器中，然后部署到任意你想部署到的环境。

### Docker的三个概念
1.镜像（Image）：类似于虚拟机中的镜像，是一个包含有文件系统的面向Docker引擎的只读模板。任何应用程序运行都需要环境，而镜像就是用来提供这种运行环境的。例如一个Ubuntu镜像就是一个包含Ubuntu操作系统环境的模板，同理在该镜像上装上Apache软件，就可以称为Apache镜像。

2.容器（Container）：类似于一个轻量级的沙盒，可以将其看作一个极简的Linux系统环境（包括root权限、进程空间、用户空间和网络空间等），以及运行在其中的应用程序。Docker引擎利用容器来运行、隔离各个应用。容器是镜像创建的应用实例，可以创建、启动、停止、删除容器，各个容器之间是是相互隔离的，互不影响。注意：镜像本身是只读的，容器从镜像启动时，Docker在镜像的上层创建一个可写层，镜像本身不变。

3.仓库（Repository）：类似于代码仓库，这里是镜像仓库，是Docker用来集中存放镜像文件的地方。注意与注册服务器（Registry）的区别：注册服务器是存放仓库的地方，一般会有多个仓库；而仓库是存放镜像的地方，一般每个仓库存放一类镜像，每个镜像利用tag进行区分，比如Ubuntu仓库存放有多个版本（12.04、14.04等）的Ubuntu镜像。

### 安装
mac安装[下载地址](https://www.docker.com/docker-mac)

### 注册并登录

[注册](https://cloud.docker.com)

``` 
 登录
docker login
```
### 常用命令
1. 运行容器

`docker run -it [imageName]`

-it  参数作用是以交互模式进入容器，并打开终端。

`docker -p 8080:80 -d nginx`
-p: 端口映射：把nginx的80端口映射到宿主机的8080端口
-d: 把这个container以守护进程的方式在后台运行

. 拷贝文件到容器中
`docker cp index.html [containerId]://user/share/nginx/html`

2. 查看本地镜像
`docker images`

3. 查看容器
`docker ps`
查看运行中的容器

`docker ps -a`
-a:查看所有运行过的容器

4. 停止容器运行
`docker stop [containerId]`

5. 删除容器
`docker rm [containerId]`

5. 保存对容器的操作
`docker commit -m 'msg' [containerId] [imagesRename]`
创建新的镜像

6. 删除镜像
`docker rmi [imageId]`

```
docker build -t friendlyname .  # Create image using this directory's Dockerfile
docker run -p 4000:80 friendlyname  # Run "friendlyname" mapping port 4000 to 80
docker run -d -p 4000:80 friendlyname         # Same thing, but in detached mode
docker container ls                                # List all running containers
docker container ls -a             # List all containers, even those not running
docker container stop <hash>           # Gracefully stop the specified container
docker container kill <hash>         # Force shutdown of the specified container
docker container rm <hash>        # Remove specified container from this machine
docker container rm $(docker container ls -a -q)         # Remove all containers
docker image ls -a                             # List all images on this machine
docker image rm <image id>            # Remove specified image from this machine
docker image rm $(docker image ls -a -q)   # Remove all images from this machine
docker login             # Log in this CLI session using your Docker credentials
docker tag <image> username/repository:tag  # Tag <image> for upload to registry
docker push username/repository:tag            # Upload tagged image to registry
docker run username/repository:tag                   # Run image from a registry
```

### Dockerfile创建新镜像
``` 
FROM ubuntu
MAINTAINER gg
RUN  sed -i 's/archive.ubuntu.com/mirrors.ustc.edu.cn/g' /etc/apt/sources.list 
RUN apt-get update
RUN apt-get install -y nginx
COPY index.html /var/www/html
ENTRYPOINT ["/usr/sbin/nginx","-g","daemon off;"]
EXPOSE 80
```
FROM  base image 从哪开始

RUN 执行命令

ADD 添加文件或目录，可以包括远程文件

COPY 拷贝文件或目录

CMD 执行命令

EXPOSE 暴露端口

WORKDIR 指定运行命令的路径

MAINTAINER 维护者

ENV 设定环境变量

ENTRYPOINT 容器入口

USER 指定用户

VALUME mount point 挂载卷


`docker build -t [name] .`
-t：标签名
.：路径，当前目录下所有文件进行build
