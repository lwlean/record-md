# Docker 概念
镜像 images
容器 containers 
仓库 repository

# Docker  实质
从根本上讲，一个容器不过是一个正在运行的进程，并对其应用了一些附加的封装功能，以使其与主机和其他容器隔离。 容器隔离的最重要方面之一是每个容器都与自己的专用文件系统进行交互。 该文件系统由Docker映像提供。 映像包括运行应用程序所需的所有内容-代码或二进制文件，运行时，依赖关系以及所需的任何其他文件系统对象。

# Docker VS 虚拟机(Virtual machines)
容器在Linux上本地运行，并与其他容器共享主机的内核。 它运行一个分离的进程，不占用任何其他可执行文件更多的内存，从而使其轻巧。
​相比之下，虚拟机（VM）运行具有“虚拟机管理程序”对主机资源的虚拟访问权的操作系统。 通常，VM会产生大量开销，超出了应用程序逻辑所消耗的开销。

# Docker  优点

灵活、轻量级、多平台、松耦合、可扩容、安全

# Download and install Docker Desktop

[docker desktop for windows](https://docs.docker.com/docker-for-windows/install/)

# Docker File

```
FROM 拉取镜像
WORKDIR 在镜像中创建目录
COPY 复制当前文件到镜像目录中
RUN 执行命令 （docker build 时）
EXPOSE 开放端口
CMD 执行命令 （docker run 时）
ENV 设置环境变量
```


# Docker  command 命令

```
拉取镜像 docker pull image[:version]
查看镜像 docker images
查看容器 docker ps [-a]
删除容器 docker rm [continerID/continerName]
删除镜像 docker rmi imageID
运行镜像 docker run -it --name [continerName] [imageName:version] /bin/bash
进入运行的镜像 docker exec -it continerID /bin/bash
从容器复制文件 docker cp continerName:[文件路径] 宿主机目录
nginx挂载文件 docker run -d -p 8001:8001 -v c:/users/ll/docker/nginx/conf/nginx.conf:/etc/nginx/nginx.conf -v c:/users/ll/docker/nginx/html/index.html:/etc/nginx/html/index.html --name nginx nginx:latest
mysql挂载 docker run -d -p 3306:3306 -e MYSQL_ROOT_PASSWORD=123456 -v c:/users/ll/docker/mysql/database:/var/lib/mysql --name mysql mysql:5.7.32
redis docker run -d -p 6379:6379 --name redis redis:latest redis-server
查看镜像/容器详细信息 docker inspect dockerImageID|continerId|continerName
查看端口 docker port continerID|continerName
docker 镜像内安装ps top等工具 apt update & apt install procps
```

# Docker Network

```
C:\Users\ll>docker network ls
NETWORK ID          NAME                DRIVER              SCOPE
7ca0fd791450        bridge              bridge              local
a99f0a6598ef        host                host                local
a9f2c882edd1        none                null                local
```



# Docker Compose

用于定义和运行多容器的docker工具

```yml
version: "3.8" #版本号 分为1.x 2.x 3.x 其中3.x时对应的1.14之后的版本
services: # 启动多个服务
  mysql:
    container_name: "mysql-docker" # 容器名称
    image: "mysql:5.7.32" # 镜像名称
    ports: # 端口号
      - "3306:3306"
    environment: # 环境变量
      MYSQL_ROOT_PASSWORD: 123456
    networks: # 网络 需要自定义
      - my_work
    volumes: # 卷映射
      - "c:/users/ll/docker/mysql/database:/var/lib/mysql"

networks: # 自定义网络
  my_work:
    driver: bridge
```



