# Linux

## 网络运维相关

```
lsof -i:端口号

lsof -i:8080：查看8080端口占用
lsof abc.txt：显示开启文件abc.txt的进程
lsof -c abc：显示abc进程现在打开的文件
lsof -c -p 1234：列出进程号为1234的进程所打开的文件
lsof -g gid：显示归属gid的进程情况
lsof +d /usr/local/：显示目录下被进程开启的文件
lsof +D /usr/local/：同上，但是会搜索目录下的目录，时间较长
lsof -d 4：显示使用fd为4的进程
lsof -i -U：显示所有打开的端口和UNIX domain文件
```

```
netstat -tunlp | grep 端口号

-t (tcp) 仅显示tcp相关选项
-u (udp)仅显示udp相关选项
-n 拒绝显示别名，能显示数字的全部转化为数字
-l 仅列出在Listen(监听)的服务状态
-p 显示建立相关链接的程序名
```


```
kill -9 PID
```


## 权限

```
语法
chmod [-cfvR] [--help] [--version] mode file...
参数说明
mode : 权限设定字串，格式如下 :

[ugoa...][[+-=][rwxX]...][,...]
其中：

u 表示该文件的拥有者，g 表示与该文件的拥有者属于同一个群体(group)者，o 表示其他以外的人，a 表示这三者皆是。
+ 表示增加权限、- 表示取消权限、= 表示唯一设定权限。
r 表示可读取，w 表示可写入，x 表示可执行，X 表示只有当该文件是个子目录或者该文件已经被设定过为可执行。
其他参数说明：

-c : 若该文件权限确实已经更改，才显示其更改动作
-f : 若该文件权限无法被更改也不要显示错误讯息
-v : 显示权限变更的详细资料
-R : 对目前目录下的所有文件与子目录进行相同的权限变更(即以递归的方式逐个变更)
--help : 显示辅助说明
--version : 显示版本
```
```
chmod a+r file	给file的所有用户增加读权限
chmod a-x file	删除file的所有用户的执行权限
chmod a+rw file	给file的所有用户增加读写权限
chmod +rwx file	给file的所有用户增加读写执行权限
chmod u=rw,go= file	对file的所有者设置读写权限，清空该用户组和其他用户对file的所有权限（空格代表无权限）
chmod -R u+r,go-r docs	对目录docs和其子目录层次结构中的所有文件给用户增加读权限，而对用户组和其他用户删除读权限
chmod 664 file	对file的所有者和用户组设置读写权限, 为其其他用户设置读权限
chmod 0755 file	相当于u=rwx (4+2+1),go=rx (4+1 & 4+1)。0 没有特殊模式。
chmod 4755 file	4设置了设置用户ID位，剩下的相当于 u=rwx (4+2+1),go=rx (4+1 & 4+1)。
find path/ -type d -exec chmod a-x {} \;	删除可执行权限对path/以及其所有的目录（不包括文件）的所有用户，使用'-type f'匹配文件
find path/ -type d -exec chmod a+x {} \;	允许所有用户浏览或通过目录path/
```

`sudo chmod -R 777 *`
## bash

### 后台执行
基本方法 加&  
`./test.sh &`

shell关闭而进程不死  
`nohup ./test.sh &`