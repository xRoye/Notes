# 语法

## docker run
docker run ：创建一个新的容器并运行一个命令

##语法

```
docker run [OPTIONS] IMAGE [COMMAND] [ARG...]
OPTIONS说明：

-a stdin: 指定标准输入输出内容类型，可选 STDIN/STDOUT/STDERR 三项；

-d: 后台运行容器，并返回容器ID；

-i: 以交互模式运行容器，通常与 -t 同时使用；

-P: 随机端口映射，容器内部端口随机映射到主机的端口

-p: 指定端口映射，格式为：主机(宿主)端口:容器端口

-t: 为容器重新分配一个伪输入终端，通常与 -i 同时使用；

--name="nginx-lb": 为容器指定一个名称；

--dns 8.8.8.8: 指定容器使用的DNS服务器，默认和宿主一致；

--dns-search example.com: 指定容器DNS搜索域名，默认和宿主一致；

-h "mars": 指定容器的hostname；

-e username="ritchie": 设置环境变量；

--env-file=[]: 从指定文件读入环境变量；

--cpuset="0-2" or --cpuset="0,1,2": 绑定容器到指定CPU运行；

-m :设置容器使用内存最大值；

--net="bridge": 指定容器的网络连接类型，支持 bridge/host/none/container: 四种类型；

--link=[]: 添加链接到另一个容器；

--expose=[]: 开放一个端口或一组端口；

--volume , -v: 绑定一个卷
```


### 实例
使用docker镜像nginx:latest以后台模式启动一个容器,并将容器命名为mynginx。  
`docker run --name mynginx -d nginx:latest`  

使用镜像nginx:latest以后台模式启动一个容器,并将容器的80端口映射到主机随机端口。  
`docker run -P -d nginx:latest`  

使用镜像 nginx:latest，以后台模式启动一个容器,将容器的 80 端口映射到主机的 80 端口,主机的目录 /data 映射到容器的 /data。  
`docker run -p 80:80 -v /data:/data -d nginx:latest`  

绑定容器的 8080 端口，并将其映射到本地主机 127.0.0.1 的 80 端口上。  
`$ docker run -p 127.0.0.1:80:8080/tcp ubuntu bash`  

使用镜像nginx:latest以交互模式启动一个容器,在容器内执行/bin/bash命令。
```
runoob@runoob:~$ docker run -it nginx:latest /bin/bash
root@b8573233d675:/# 
```

进入容器
```
docker attach 容器ID
或者
docker exec -it 容器ID /bin/bash 
或者
docker exec -it 容器的name bash
```

退出容器
```
exit
or
Ctrl+P+Q
```

## Docker 离线导入导出


Ref. https://www.runoob.com/docker/docker-command-manual.html

有时我们需要将一台电脑上的镜像复制到另一台电脑上使用，除了可以借助仓库外，还可以直接将镜像保存成一个文件，再拷贝到另一台电脑上导入使用。ps. 使用 `docker ps -a` 命令查看本机所有的容器.  
对于镜像的导出和导入，Docker 提供了两种方案(export+import、save+load)  
Ref. https://www.hangge.com/blog/cache/detail_2411.html


### 区别
Ref.https://zhuanlan.zhihu.com/p/649896526

Docker image 的两个主要命令 docker save 和 docker export 这两个命令看起来非常相似，但实际上它们的用途和行为是有所不同的。在本文中，我们将深入探讨 docker save 和 docker export 之间的差异，帮助你更好地使用 Docker。

1. 命令的含义

首先，我们需要了解这两个命令的含义。简而言之，docker save 用于将 Docker image 打包成.tar 文件，而 docker export 则用于将指定的容器转换成独立的.tar 文件。

2. 命令的形式

docker save 命令的格式如下：

　　`docker save [OPTIONS] IMAGE [IMAGE...]`

而 docker export 命令的格式如下：

　　`docker export [OPTIONS] CONTAINER`

需要注意的是，docker save 命令需要指定一个或多个 IMAGE，而 docker export 命令只需要指定一个 CONTAINER。

3. 使用场景

要想正确理解这两个命令之间的差异，我们需要了解它们的主要使用场景。

docker save 命令通常用于在不同的 Docker 主机之间迁移 Docker image，或者将它们备份到本地系统（如外部硬盘或云存储提供商）以供以后使用。这个命令经常用于离线环境下安装 Docker image，并且在应用程序的生命周期内保存和复制 Docker image。

docker export 命令则用于将单个容器作为.tar 文件导出。这个命令通常用于快速备份或转移某个容器，或在需要将容器升级到新的版本之前，对其进行测试。

4. 行为

虽然两个命令的目标和格式不同，但它们之间的行为也非常不同。

docker save 命令会将 Docker image 打包成一个压缩的.tar 文件。这意味着，您可以使用 Docker load 命令将其重新导入 Docker 主机或 Docker Registry。这个命令会保留 Docker image 的所有元数据，包括 image 的标签，以及为此 image 创建的任何镜像。

docker export 命令会将当前正在运行的容器快照导出到一个.tar 文件中。这意味着，您只能在本地 Docker 主机上恢复该容器，并且它将不包含任何元数据，如 image 的标签。此外，你不能使用 docker load 命令将其重新导入 Docker 主机或 Docker Registry 中。

5. 总结

在这篇文章中，我们介绍了 Docker image 打包的两种不同方式：docker save 和 docker export。我们深入探讨了这两个命令之间的区别，包括命令格式、使用场景和行为。

需要记住的是，docker save 用于轻松迁移 Docker image 和备份 image 的元数据，而 docker export 用于快速备份单个容器。通过深入了解它们之间的差异，你将更好地理解如何使用这些命令来管理 Docker image 和容器。

### save load

#### docker save : 将指定镜像保存成 tar 归档文件。

语法  
`docker save [OPTIONS] IMAGE [IMAGE...]`
OPTIONS 说明：  
-o :输出到的文件。

eg.将镜像 runoob/ubuntu:v3 生成 my_ubuntu_v3.tar 文档
```
runoob@runoob:~$ docker save -o my_ubuntu_v3.tar runoob/ubuntu:v3
runoob@runoob:~$ ll my_ubuntu_v3.tar
-rw------- 1 runoob runoob 142102016 Jul 11 01:37 my_ubuntu_v3.ta
```

#### docker load : 导入使用 docker save 命令导出的镜像。
语法 `docker load [OPTIONS]`
OPTIONS 说明：  
--input , -i : 指定导入的文件，代替 STDIN。  
--quiet , -q : 精简输出信息。  

eg.
导入镜像：
```
$ docker image ls

REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE

$ docker load < busybox.tar.gz

Loaded image: busybox:latest
$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
busybox             latest              769b9341d937        7 weeks ago         2.489 MB

$ docker load --input fedora.tar

Loaded image: fedora:rawhide

Loaded image: fedora:20

$ docker images

REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
busybox             latest              769b9341d937        7 weeks ago         2.489 MB
fedora              rawhide             0d20aec6529d        7 weeks ago         387 MB
fedora              20                  58394af37342        7 weeks ago         385.5 MB
fedora              heisenbug           58394af37342        7 weeks ago         385.5 MB
fedora              latest              58394af37342        7 weeks ago         385.5 MB
```


### export  import

#### docker export :将文件系统作为一个tar归档文件导出到STDOUT。
语法 `docker export [OPTIONS] CONTAINER`   
-o :将输入内容写到文件。  
eg.
```
runoob@runoob:~$ docker export -o mysql-`date +%Y%m%d`.tar a404c6c174a2
runoob@runoob:~$ ls mysql-`date +%Y%m%d`.tar
mysql-20160711.tar

```

#### docker import : 从归档文件中创建镜像。
语法 `docker import [OPTIONS] file|URL|- [REPOSITORY[:TAG]]`  
OPTIONS说明：  
-c :应用docker 指令创建镜像；  
-m :提交时的说明文字；

eg. 从镜像归档文件my_ubuntu_v3.tar创建镜像，命名为runoob/ubuntu:v4
```
runoob@runoob:~$ docker import  my_ubuntu_v3.tar runoob/ubuntu:v4  
sha256:63ce4a6d6bc3fabb95dbd6c561404a309b7bdfc4e21c1d59fe9fe4299cbfea39
runoob@runoob:~$ docker images runoob/ubuntu:v4
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
runoob/ubuntu       v4                  63ce4a6d6bc3        20 seconds ago      142.1 MB
```



# Tips
## docker的设置

docker换源：手动创建文件 /etc/docker/daemon.json

写入
```
{

    "registry-mirrors": [

"https://mirror.baidubce.com/", 

"https://ccr.ccs.tencentyun.com/",

"http://hub-mirror.c.163.com/",

"https://dockerproxy.com/",

"https://ung2thfc.mirror.aliyuncs.com/"

]

}
```
重启docker  ： `sudo service docker restart`

验证配置：`sudo docker info | grep Mirrors -A 4`

```
 Registry Mirrors:

 https://ccr.ccs.tencentyun.com/

  http://hub-mirror.c.163.com/

  https://mirror.baidubce.com/

  https://dockerproxy.com/

  https://ung2thfc.mirror.aliyuncs.com/

 Live Restore Enabled: false
```
## 安装portainer：
```
sudo docker search portainer #先search

sudo docker pull portainer/portainer-ce:latest #新版是社区版了，-ce

sudo docker run -d  --name portainer -p 9000:9000 -v /var/run/docker.sock:/var/run/docker.sock -v /app/portainer_data:/data --restart always --privileged=true portainer/portainer-ce:latest
```
访问手机ip:9000即可

## 安装vsftpd 未验证

```
cd /home
mkdir ftp #创建ftp目录

#pull image
docker search vsftpd
docker pull fauria/vsftpd
docker images

ifconfig / ip addr

#run
docker run -d -v /:/var/run/docker.sock -v /app/portainer_data:/data --restart always --privileged=true portainer/portainer-ce:latest
--name portainer -p 9000:9000 
```

### 启动命令拆解

```
docker run -d #运行容器
-v /home/ftp:/home/ftp #-v(宿主目录:容器目录) 目录挂载，绑定到一起
-p 20:20 -p 60021:21 -p 21100-21110 #-p(宿主端口:容器端口) 端口映射
-e FTP_USER=root -e FTP_PASS=root #指定环境变量
-name vsftpd fauria/vsftpd #命名容器
```

## 修改dns可以提升pull的速度

修改 /etc/resolv.conf  nameserver（dns服务）为阿里云的dns

nameserver 223.5.5.5 

# 实例

* [Docker 打包 vue3 项目](/markdown/Docker_vue3.md)

* [Docker部署Django 之 单容器部署Django + Uwsgi](/markdown/Docker_django_uwsgi.md)