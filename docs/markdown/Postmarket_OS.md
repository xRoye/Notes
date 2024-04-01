# Introduction

## Related Works & Ref
[PostmarketOS官方安装说明 Redmi2为例](https://wiki.postmarketos.org/wiki/Xiaomi_Redmi_2_(xiaomi-wt88047))  

[红米2 安装Linux: postmarketOS(基于Alpine)及后续玩法](https://www.bilibili.com/read/cv17390817)

## Main contribution
根据上述两篇进行记录与补充。  
重点贡献在于参考文献[2]中，安装docker后启动失败问题的处理

同样投稿在bilibili专栏  
[红米2 安装Linux: postmarketOS 以及docker启动失败的解决方法](https://www.bilibili.com/read/cv29866221)


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


# 安装 vsftpd
```
sudo apk add vsftpd
sudo service vsftpd start #开启SSH Server 服务
sudo rc-update add vsftpd # 开机启动SSHD 服务

sudo service vsftpd restart

sudo vsftpd info #查看状态

sudo adduser -h /home/ftpuser -s /sbin/nologin ftpuser #新建ftpuser用户，且不允许登录
sudo passwd ftpuser #设定密码 helloworld

```

配置文件在  
`/etc/vsftpd/vsftpd.conf`

我自己的配置：  
```
anonymous_enable=NO
local_enable=YES
write_enable=YES
dirmessage_enable=YES
xferlog_enable=YES
connect_from_port_20=YES
chroot_local_user=YES
chroot_list_enable=YES
chroot_list_file=/etc/vsftpd/chroot_list
listen=YES
allow_writeable_chroot=YES
pasv_min_port=8888#指定被动状态端口范围，与防火墙配置对应
pasv_max_port=8899#
```
示例1：

除了chroot_list列表中的用户不被限制外，其他用户都被限制在其主目录下  (chroot_list文件需要自己创建) 
```
chroot_local=YES
chroot_list_enable=YES
chroot_list_file=/etc/vsftpd/chroot_list
```

```
anonymous_enable=NO  
local_enable=YES  
local_root=/home/ftpuser #指定本地和虚拟用户访问目录  
write_enable=YES  

userlist_enable=YES  
userlist_deny=NO #只能user_list文件中的用户访问ftp  

allow_writeable_chroot=YES #500 OOPS chroot错误的解决方法
```
## 注意，sshd自带一个sftp，我把sftp关了

 
# 防火墙 nftables —— 以FTP配置为例

现有教程基本都是讲iptables 极少有讲nftables配置的

https://wiki.nftables.org/wiki-nftables/index.php/Main_Page

`sudo nft list ruleset`
```
table inet filter {
        chain input {
                type filter hook input priority filter; policy drop;
                ...
        }

        chain forward {
                type filter hook forward priority filter; policy drop;
                ...
        }

        chain output {
                type filter hook output priority filter; policy accept;
        }
}
```

带handle的list，方便删除
`sudo nft -a list ruleset` 

`sudo service nftables status`

`nft add rule filter output ip daddr 8.8.8.8 counter`  
Where filter is the table 
and output is the chain. 
The example above adds a rule to match all packets seen by the output chain whose destination is 8.8.8.8, 
in case of matching it updates the rule counters. Note that counters are optional in nftables.

For those familiar with iptables, the rule appending is equivalent to -A command in iptables.

在 nftables 中，ipv4 和 ipv6 协议可以被合并到一个单一的地址簇 inet 中，使用了 inet 地址簇，就不需要分别为 ipv4 和 ipv6 指定两个不同的规则了
使用：

`sudo nft add rule inet filter input tcp dport {20,21,8888-8899} accept comment \"Accept FTP\"`
`sudo nft add rule inet filter output tcp dport {20,8888-8899} accept comment \"Accept FTP\"`

`sudo service nftables restart`
 
`sudo nft delete rule inet filter input handle 36`

以上所有示例中的规则都是临时的，要想永久生效，我们可以将规则备份，重启后自动加载恢复，其实 nftables 的 systemd 服务就是这么工作的。


在 CentOS 8 中，nftables.service 的规则被存储在 /etc/nftables.conf 中，其中 include 一些其他的示例规则，一般位于 /etc/sysconfig/nftables.conf 文件中，但默认会被注释掉。备份规则：  `nft list ruleset > /root/nftables.conf`加载恢复：  `nft -f /root/nftables.conf`  

实际在 POS 中 ，规则写在 /etc/nftables.nft 中
其中引用了如下目录文件  
```
# The state of stateful objects saved on the nftables service stop.
include "/var/lib/nftables/*.nft"
# Rules
include "/etc/nftables.d/*.nft"
```
在 /etc/nftables.d/ touch 一个新文件 ftp.nft
nano 写入
```
table inet filter {
        chain input {
            tcp dport { 20-21, 8888-8899 } accept comment "Accept FTP"
        }
        chain output{
            tcp dport { 20, 8888-8899 } accept comment "Accept FTP"
        }
}
```
终于搞定FTP


# Samba

Ref. https://zhuanlan.zhihu.com/p/375925918

## 安装
```
sudo apk add samba

sudo service samba start #开启samba 服务
sudo rc-update add samba # 开机启动samba 服务

sudo service vsftpd restart

```
## 配置

`sudo nano /etc/samba/smb.conf`
```
#1.全局部分参数设置：
[global]
        #与主机名相关的设置
        workgroup = zkhouse  <==工作组名称
        netbios name = zkserver   <==主机名称，跟hostname不是一个概念，在同一个组中，netbios name必须唯一
        serverstring = this is a test samba server <==说明性文字，内容无关紧要
        #与登录文件有关的设置
        log file = /var/log/samba/log.%m   <==日志文件的存储文件名，%m代表的是client端Internet主机名，就是hostname
        max log size = 50      <==日志文件最大的大小为50Kb
        #与密码相关的设置
        security = share       <==表示不需要密码，可设置的值为share、user和server
        passdb backend = tdbsam
        #打印机加载方式
        load printer = no <==不加载打印机
-----------------------------------------------------------
#2.共享资源设置方面：将旧的注释掉，加入新的
#先取消[homes]、[printers]的项目，添加[temp]项目如下
[temp]              <==共享资源名称
        comment = Temporary file space <==简单的解释，内容无关紧要
        path = /tmp     <==实际的共享目录
        writable = yes    <==设置为可写入
        browseable = yes   <==可以被所有用户浏览到资源名称，
        guest ok = yes    <==可以让用户随意登录
```

Linux的用户密码和samba的用户密码并不是一码子事，只是samba的用户必须是Linux的用户，因此需要将smbuser这个账户添加到samba的用户数据库，否则无法访问共享目录，执行如下命令:

pwd 147147
```
sudo smbpasswd -a user
New SMB password:
Retype new SMB password:
Added user user.
```

重启服务

## 防火墙 nftables

Samba端口说明
Samba服务所使用的端口和协议： 

1）Port 137 (UDP) - NetBIOS 名字服务 ； nmbd  
2）Port 138 (UDP) - NetBIOS 数据报服务  
3）Port 139 (TCP) - 文件和打印共享 ； smbd （基于SMB(Server Message Block)协议，主要在局域网中使用，文件共享协议）  
4）Port 389 (TCP) - 用于 LDAP (Active Directory Mode)  
5）Port 445 (TCP) - NetBIOS服务在windos 2000及以后版本使用此端口, (Common Internet File System，CIFS，它是SMB协议扩展到Internet后，实现Internet文件共享)  
6）Port 901 (TCP) - 用于 SWAT，用于网页管理Samba    

这里直接把相关端口全开放了
```
/etc/nftables.d$ sudo touch samba.nft
/etc/nftables.d$ sudo nano samba.nft
```

samba.nft
```
table inet filter {
        chain input {
            tcp dport { 137-139,389,445,901 } accept comment "Accept Samba"
        }
        chain output{
            tcp dport { 137-139,389,445,901 } accept comment "Accept Samba"
        }
}

```

`/etc/nftables.d$ sudo service nftables restart`

搞定