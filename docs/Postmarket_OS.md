# Ref

[PostmarketOS官方安装说明 Redmi2为例](https://wiki.postmarketos.org/wiki/Xiaomi_Redmi_2_(xiaomi-wt88047))  

[红米2 安装Linux: postmarketOS(基于Alpine)及后续玩法](https://www.bilibili.com/read/cv17390817)

主要根据上述两篇进行记录与补充。重点在于参考文献[2]中，安装docker后启动失败问题的处理


# Install
## 更新firmware

依照上述任意一篇参考流程更新firmware。

个人建议参考<https://wiki.lineageos.org/devices/wt88047/install/#updating-firmware>（第一篇的链接指向）

up没有挂梯子，在androidfilehost 下载失败，虽然有点费事，但是可以直接刷入同版本的miui完整包更新firmware。

但是up之前就是LineageOS，更新过firmware，所以这步就跳过了

## 安装ln2d

<https://github.com/msm8916-mainline/lk2nd/releases>

在上面链接下载最新的 lk2nd-msm8916.img 

刷入lk2nd指令如下：  
```
fastboot flash boot lk2nd-msm8916.img
fastboot reboot
```
没遇到问题 

## 安装postmarketOS

在 <https://postmarketos.org/download/> 选择Redmi2最新稳定版下载

下载提供了多个桌面版本，每个版本都有xxx.img.xz 和 -boot.img.xz两个压缩包，可以通过指令unxz解压，windows环境可以直接使用压缩软件打开解压得到两个 .img文件

第一次安装的gnome版本，异常卡顿，完全没法用（v23.12），改成phosh就正常了

虽然两篇参考文献，一个没有说要刷boot，一个说新版ln2d可以不刷boot，up测试了下，刷boot与不刷boot似乎确实都行，但是既然提供了boot就刷上了。。

主要根据 <https://wiki.postmarketos.org/wiki/Qualcomm_Snapdragon_410/412_(MSM8916)#Installation> 进行安装

下面的命令自行更改文件名
```
fastboot flash boot xxxxxxx-xiaomi-wt88047-boot.img

fastboot flash userdata xxxxxxx-xiaomi-wt88047.img

fastboot erase system

fastboot reboot
```

# 安装后
## Login
默认用户名：user 密码 147147 PIN 147147

## 开启SSH
```
sudo service sshd start #开启SSH Server 服务
sudo rc-update add sshd # 开机启动SSHD 服务
```

连wifi后

ifconfig #查看当前ip

后续操作直接在电脑进行

ssh user@ip



## 编辑源

`sudo vi /etc/apk/repositories`

根据自己的版本添加源，建议把原来的直接开头加#注释掉

### vi 使用参考
```
按i进行编辑

按esc退出编辑

输入:wq回车保存

sudo apk update #更新软件包信息

sudo apk upgrade #更新软件

更新完最好重启一下 sudo reboot
```

## 安装docker

先顺手装一个nano  ：`sudo apk add nano`
```
sudo apk add docker #安装docker

sudo service docker start #启动docker服务

sudo rc-update add docker default #设置docker为自启动 
```
根据上述方法，流程没有问题，但是启动失败

# Docker启动问题

先说症状：

eg1: sudo service docker status 过一会儿就自动stopped

eg2: sudo docker info 

         Server:ERROR: Cannot connect to the Docker daemon at unix:///var/run/docker.sock. Is the docker daemon running?

具体失败的日志在  /var/log/docker.log

failed to start daemon: Error initializing network controller: error creating default "bridge" network: Failed to Setup IP tables: Unable to enable NAT rule:  (iptables failed: iptables --wait -t nat -I POSTROUTING -s 172.17.0.0/16 ! -o docker0 -j MASQUERADE: Warning: Extension MASQUERADE revision 0 not supported, missing kernel module?

iptables v1.8.10 (nf_tables):  RULE_INSERT failed (No such file or directory): rule in chain POSTROUTING

(exit status 4))

实际就是指令
`iptables --wait -t nat -I POSTROUTING -s 172.17.0.0/16 ! -o docker0 -j MASQUERADE`
执行失败。

## Docker启动失败问题分析：

<https://github.com/lisenet/docker-openvpn/issues/4>

就是iptables的问题，在iptables 1.8之后会分为两个部分，即iptables-legacy 和 iptables-nft，

可分别用`iptables-legacy -V`  和 `iptables -V` 查看。

上面的报错日志也说明了就是默认nft版本iptables v1.8.10 (nf_tables)的异常


## Docker启动失败解决方法：

参考 <https://github.com/lisenet/docker-openvpn/issues/4>

使用 legacy 版本的 iptables
```
sudo apk add iptables-legacy #安装legacy 版本的 iptables

sudo mv /sbin/iptables /sbin/iptables.bak #移除默认的 iptables

(或者直接删掉： rm /sbin/iptables #删除默认的 iptables)

sudo ln -s /sbin/iptables-legacy /sbin/iptables #链接legacy
```


再次启动Docker，问题解决 


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
安装portainer：
```
sudo docker search portainer #先search

sudo docker pull portainer/portainer-ce:latest #新版是社区版了，-ce

sudo docker run -d  --name portainer -p 9000:9000 -v /var/run/docker.sock:/var/run/docker.sock -v /app/portainer_data:/data --restart always --privileged=true portainer/portainer-ce:latest
```
访问手机ip:9000即可



## 修改dns可以提升pull的速度

修改 /etc/resolv.conf  nameserver（dns服务）为阿里云的dns

nameserver 223.5.5.5 

## Phosh: How to permanently set display scaling
Display scaling will revert back to default after reset on Phosh-based OSs such as Mobian, PostmarketOS Phosh, ect.

To make changes permanent the reference configuration needs to be copied to /etc/phosh/ with the scaling for the Pinephone’s display set as desired.

Instructions:
```
sudo mkdir /etc/phosh/
sudo cp /usr/share/phosh/phoc.ini /etc/phosh/
sudo vi /etc/phosh/phoc.ini
\# Uncomment the DSI-1 output section:
\[output:DSI-1\]
scale = 2
\# Change scale to desired value (ex: scale = 1.5 means 150%)
\# Save and reboot
```