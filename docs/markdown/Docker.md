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