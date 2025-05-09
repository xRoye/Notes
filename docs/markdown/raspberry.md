
`sudo nano /etc/apt/sources.list`

```
deb https://mirrors.tuna.tsinghua.edu.cn/debian bookworm main contrib non-free non-free-firmware
deb https://mirrors.tuna.tsinghua.edu.cn/debian-security/ bookworm-security main contrib non-free non-free-firmware
deb https://mirrors.tuna.tsinghua.edu.cn/debian bookworm-updates main contrib non-free non-free-firmware
#deb http://deb.debian.org/debian bookworm main contrib non-free non-free-firmware
#deb http://deb.debian.org/debian-security/ bookworm-security main contrib non-free non-free-firmware
#deb http://deb.debian.org/debian bookworm-updates main contrib non-free non-free-firmware
# Uncomment deb-src lines below then 'apt-get update' to enable 'apt-get source'
#deb-src http://deb.debian.org/debian bookworm main contrib non-free non-free-firmware
#deb-src http://deb.debian.org/debian-security/ bookworm-security main contrib non-free non-free-firmware
#deb-src http://deb.debian.org/debian bookworm-updates main contrib non-free non-free-firmware
```

`sudo apt update`

`sudo apt upgrade`

#### mount usb

`sudo fdisk -l`

```
Disk /dev/sda: 4.83 GiB, 5182062592 bytes, 10121216 sectors
Disk model: Flash Disk
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0xe5416279

Device     Boot Start      End  Sectors  Size Id Type
/dev/sda1        8192 10121215 10113024  4.8G  c W95 FAT32 (LBA)
```

`sudo mkdir /mnt/usb`

`sudo mount /dev/sda1 /mnt/usb`

`sudo umount /mnt/usb`

#### docker install

ref. https://docs.docker.com/engine/install/debian/

##### Uninstall old versions  
`for pkg in docker.io docker-doc docker-compose podman-docker containerd runc; do sudo apt-get remove $pkg; done`

##### Install using the apt repository

1. Set up Docker's apt repository.


aliyun
```
# 添加 Docker 官方 GPG key:
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL http://mirrors.aliyun.com/docker-ce/linux/debian/gpg -o /etc/apt/keyrings/docker_aliyun.asc
sudo chmod a+r /etc/apt/keyrings/docker_aliyun.asc

# 添加仓库到 Apt 源:
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker_aliyun.asc] http://mirrors.aliyun.com/docker-ce/linux/debian \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker_aliyun.list > /dev/null
sudo apt-get update
```

origin
```
# Add Docker's official GPG key:
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/debian/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/debian \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
``` 

2. Install the Docker packages.

`sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin`

3. 换源

编辑 daemon.json 如果没有就手动创建 (实际上nano会自动创建)
` sudo nano /etc/docker/daemon.json`

~~ 使用阿里云的镜像加速  ~~  不好用了
~~ https://cr.console.aliyun.com/cn-chengdu/instances/mirrors ~~  

默认权限  
```
raspberry@raspberrypi:/etc/docker $ ls -l
total 4
-rw-r--r-- 1 root root 202 Jan 27 22:42 daemon.json
```

配置该文件的权限，所有用户可读写，试图解决配置不生效  
`sudo chmod 666 daemon.json`  
没啥用， search还是报错，还是源的问题
```
raspberry@raspberrypi:~ $ sudo docker search hello-world
Error response from daemon: Get "https://index.docker.io/v1/search?q=hello-world&n=25": dial tcp [2a03:2880:f136:83:face:b00c:0:25de]:443: i/o timeout
raspberry@raspberrypi:~ $
```

```
{
    "registry-mirrors": [
    	"https://docker-0.unsee.tech",
        "https://docker-cf.registry.cyou",
        "https://docker.1panel.live"
    ]
}
```

重启服务
```
sudo systemctl daemon-reload
sudo systemctl restart docker
```
`sudo docker info`

还是不会走配置的镜像

3. Verify that the installation is successful by running the hello-world image:

`sudo systemctl status docker`  

`sudo docker search hello-world`

自然不行

```
raspberry@raspberrypi:/etc/default $ sudo docker pull docker.unsee.tech/hello-world
Using default tag: latest
Error response from daemon: Get "https://docker.unsee.tech/v2/": net/http: request canceled while waiting for connection (Client.Timeout exceeded while awaiting headers)
```

使用临时地址为什么会在url加v2？？？？

结果还是源的问题，我tm...   ref. https://cloud.tencent.com/developer/article/2485043


`sudo docker run hello-world`

查看本地镜像  
`sudo docker images`

删除本地镜像  
`sudo docker rmi xxxx`

查看运行中的容器  
`sudo docker ps`

如果您想查看所有容器，无论它们的状态如何，您可以添加 -a 或 --all 选项：  
`sudo docker ps -a`

启动和停止容器

除了查看容器，你可能还需要启动或停止容器。启动一个已经存在的容器可以使用 docker start 命令，后面跟上容器 ID 或名称：  
`sudo docker start <容器ID或名称>`

停止一个运行中的容器可以使用 docker stop 命令，同样是后面跟上容器 ID 或名称：  
`sudo docker stop <容器ID或名称>`


4. Uninstall Docker Engine

```
Uninstall the Docker Engine, CLI, containerd, and Docker Compose packages:
`sudo apt-get purge docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin docker-ce-rootless-extras`

Images, containers, volumes, or custom configuration files on your host aren't automatically removed. To delete all images, containers, and volumes:
`sudo rm -rf /var/lib/docker`
`sudo rm -rf /var/lib/containerd`

Remove source list and keyrings
`sudo rm /etc/apt/sources.list.d/docker.list`
`sudo rm /etc/apt/keyrings/docker.asc`

You have to delete any edited configuration files manually.
```