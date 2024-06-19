# Docker部署之 mysql

Ref. https://www.runoob.com/docker/docker-install-mysql.html

`docker search mysql` 
命令来查看可用版本

这里我们拉取官方的最新版本的镜像：
`docker pull mysql:latest`

使用以下命令来查看是否已安装了 mysql：
`docker images`

安装完成后，我们可以使用以下命令来运行 mysql 容器：

`docker run -itd --name mysql -p 3306:3306 -e MYSQL_ROOT_PASSWORD=123456 mysql`

指定网卡，指定hostname，数据持久化： (最好在本地提前创建好持久化对应目录) 

**需要注意，在重新部署数据库时，需要手动删除持久化目录内的内容，否则新部署的数据库将不会重新初始化**


windows：  
`docker run -itd --name mysql -p 3306:3306 --network my-net -v D:\Learn\Docker\mysql\data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=1234 mysql`

linux：  
`docker run -itd --name mysql -p 3306:3306 --network my-net -v /home/workspace/docker-mysql/mysql/data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=1234 mysql`


关于网卡配置参考 [Docker-网络配置](/markdown/Docker.md#docker-网络配置)


参数说明：
-v /path/to/mysql-data:/var/lib/mysql：将容器内的/var/lib/mysql目录映射到宿主机的/path/to/mysql-data目录，实现数据持久化
-p 3306:3306 ：映射容器服务的 3306 端口到宿主机的 3306 端口，外部主机可以直接通过 宿主机ip:3306 访问到 MySQL 的服务。  
-e MYSQL_ROOT_PASSWORD=123456：设置 MySQL 服务 root 用户的密码。


通过 docker ps 命令查看是否运行成功

本机可以通过 root 和密码 123456 访问 MySQL 服务。


#进入容器  
`docker exec -it mysql bash`  
#登录mysql  
`mysql -h localhost -u root -p`  
`mysql -u root -p`

但是这种方式不是很方便，我们想要初始化一个mysql镜像

Dockerfile：
```
FROM mysql:latest
LABEL org.opencontainers.image.authors="Roye@abc.def"
COPY ./sql  /tmp/sql
RUN mv /tmp/sql/*.sql /docker-entrypoint-initdb.d
RUN rm -rf /tmp/sql
```

当Mysql容器首次启动时，会在 /docker-entrypoint-initdb.d目录下扫描 .sh，.sql，.sql.gz类型的文件。如果这些类型的文件存在，将执行它们来初始化一个数据库。这些文件会按照字母的顺序执行。

在数据库DDL脚本中声明并指定使用数据库
```
CREATE DATABASE IF NOT EXISTS mydatabase;
USE mydatabase;
```

然后
`docker build -t mysql8 .`


**再次提醒需要注意，在重新部署数据库时，需要手动删除持久化目录内的内容，否则新部署的数据库将不会重新初始化**
windows：  
`docker run -itd --name mysql -p 3306:3306 --network my-net -v D:\Learn\Docker\mysql\data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=1234 mysql8`

linux：  
`docker run -itd --name mysql -p 3306:3306 --network my-net -v /home/workspace/docker-mysql/mysql/data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=1234 mysql8`


报错：RuntimeError: 'cryptography' package is required for sha256_password or caching_sha2_password auth methods  
解决方式：  
`pip install cryptography`
或者在后端打包时的requirements中加入  
`cryptography==42.0.8`  
这是由于mysql新的登录加密验证方式造成的，具体可以通过'mysql'库中user表： 
`SELECT plugin FROM user`  
老版本或者弱加密应显示`mysql_native_password`,就不会报cryptography错误  
8.0以上版本默认会使用`caching_sha2_password`
