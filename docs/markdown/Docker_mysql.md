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

指定网卡，指定hostname，数据持久化：  
`docker run -itd --name mysql -p 3306:3306 --network my-net -v /home/workspace/docker-mysql/mysql/data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=123456 mysql`

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




