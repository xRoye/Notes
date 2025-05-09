# Raspberry Pi

## Raspberry Pi OS 

https://www.raspberrypi.com/software/operating-systems/

使用 Raspberry Pi OS (Legacy) Lite, 64-bit   
对应 Debian 版本 Debian 11 (bullseye)

### 安装
没啥好说的，官网下完，用镜像烧录器装上就得了  
ssh 最好还是在安装完新建一个ssh的文件
ip地址用 'ip' 表示  

### 高级设置
`sudo raspi-config`
### 换源

先换个清华源  
https://mirrors.tuna.tsinghua.edu.cn/help/raspbian/  
aarch64 架构直接看下面的 debian

https://mirrors.tuna.tsinghua.edu.cn/help/raspberrypi/  
编辑 `/etc/apt/sources.list.d/raspi.list` 文件:  
`deb https://mirrors.tuna.tsinghua.edu.cn/raspberrypi/ bullseye main`

https://mirrors.tuna.tsinghua.edu.cn/help/debian/  
```
cd /etc/apt
sudo cp sources.list sources.list.bak
sudo nano sources.list
```  
```
# 默认注释了源码镜像以提高 apt update 速度，如有需要可自行取消注释
deb https://mirrors.tuna.tsinghua.edu.cn/debian/ bullseye main contrib non-free
# deb-src https://mirrors.tuna.tsinghua.edu.cn/debian/ bullseye main contrib non-free

deb https://mirrors.tuna.tsinghua.edu.cn/debian/ bullseye-updates main contrib non-free
# deb-src https://mirrors.tuna.tsinghua.edu.cn/debian/ bullseye-updates main contrib non-free

deb https://mirrors.tuna.tsinghua.edu.cn/debian/ bullseye-backports main contrib non-free
# deb-src https://mirrors.tuna.tsinghua.edu.cn/debian/ bullseye-backports main contrib non-free

# 以下安全更新软件源包含了官方源与镜像站配置，如有需要可自行修改注释切换
deb https://security.debian.org/debian-security bullseye-security main contrib non-free
# deb-src https://security.debian.org/debian-security bullseye-security main contrib non-free
```
### 服务相关指令

查看所有服务运行状态`sudo systemctl list-units --all --type=service`

列出服务自启动状态 +为自启动 `service --status-all`

### Cpolar
Ref https://www.cpolar.com/blog/linux-system-installation-cpolar

安装  
`curl -L https://www.cpolar.com/static/downloads/install-release-cpolar.sh | sudo bash`

验证token `cpolar authtoken xxxxxxx`

向系统添加服务 `sudo systemctl enable cpolar`

启动cpolar服务 `sudo systemctl start cpolar`

查看服务状态 `sudo systemctl status cpolar`

启动成功后，在 ip:9200 可进入cpolar管理后台

### Zerotier
ref. https://www.zerotier.com/download/

安装`curl -s https://install.zerotier.com | sudo bash`

查看服务状态 `sudo systemctl status zerotier-one`

服务默认自动添加自启动
~~向系统添加服务 `sudo systemctl enable cpolar`~~

加入网络 ，操作成功则返回 “200 join OK”   `sudo zerotier-cli join ###########`

查看当前连接的网络，如果列表中出现 Network ID、Name 说明连接成功，后台分配好IP后再查看IP地址也会出现。
`sudo zerotier-cli listnetworks`
安装``

#### 网络配置
zerotier 网桥 https://docs.zerotier.com/bridging/
