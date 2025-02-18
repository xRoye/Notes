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
## 权限 chmod

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

## chroot 与 proot

#### chroot(change root)
ref. https://www.cnblogs.com/sparkdev/p/8556075.html
chroot，即 change root directory (更改 root 目录)。在 linux 系统中，系统默认的目录结构都是以 /，即以根 (root) 开始的。而在使用 chroot 之后，系统的目录结构将以指定的位置作为 / 位置。

##### 基本语法
chroot NEWROOT [COMMAND [ARG]...]


##### 为什么要使用 chroot 命令
1. 增加了系统的安全性，限制了用户的权力：
在经过 chroot 之后，在新根下将访问不到旧系统的根目录结构和文件，这样就增强了系统的安全性。一般会在用户登录前应用 chroot，把用户的访问能力控制在一定的范围之内。

2. 建立一个与原系统隔离的系统目录结构，方便用户的开发：
使用 chroot 后，系统读取的是新根下的目录和文件，这是一个与原系统根下文件不相关的目录结构。在这个新的环境中，可以用来测试软件的静态编译以及一些与系统不相关的独立开发。

3. 切换系统的根目录位置，引导 Linux 系统启动以及急救系统等：
chroot 的作用就是切换系统的根位置，而这个作用最为明显的是在系统初始引导磁盘的处理过程中使用，从初始 RAM 磁盘 (initrd) 切换系统的根位置并执行真正的 init。

#### proot 
ref. https://proot-me.github.io/
ref. https://juejin.cn/post/7197337416096038967

PRoot 是 chroot、mount --bind 和 binfmt_misc 的用户态实现。用户不需要拥有系统特权就可以在任意目录建立一个新的根文件系统。从而在建立的根文件系统内做任何事情。也可以借助QEMU user-mode甚至能够运行其他CPU构架的程序。
从技术上来说，PRoot是依靠ptrace机制实现的。ptrace允许程序在没有拿到系统特权（root）时，父进程观察并修改子进程的系统调用，这也是PRoot的核心原理。本文主要也是分析这一机制。


##  glibc 和 libc
ref. https://blog.csdn.net/yasi_xi/article/details/9899599
glibc 和 libc 都是 Linux 下的 C 函数库。 
libc 是 Linux 下的 ANSI C 函数库；glibc 是 Linux 下的 GUN C 函数库。 

##### ANSI C 和 GNU C 有什么区别呢？ 

ANSI C 函数库是基本的 C 语言函数库，包含了 C 语言最基本的库函数。这个库可以根据头文件划分为 15 个部分，其中包括： 
1. <ctype.h>：包含用来测试某个特征字符的函数的函数原型，以及用来转换大小写字母的函数原型；
2. <errno.h>：定义用来报告错误条件的宏；
3. <float.h>：包含系统的浮点数大小限制；
4. <math.h>：包含数学库函数的函数原型；
5. <stddef.h>：包含执行某些计算 C 所用的常见的函数定义；
6. <stdio.h>：包含标准输入输出库函数的函数原型，以及他们所用的信息；
7. <stdlib.h>：包含数字转换到文本，以及文本转换到数字的函数原型，还有内存分配、随机数字以及其他实用函数的函数原型；
8. <string.h>：包含字符串处理函数的函数原型；
9. <time.h>：包含时间和日期操作的函数原型和类型；
10. <stdarg.h>：包含函数原型和宏，用于处理未知数值和类型的函数的参数列表；
11. <signal.h>：包含函数原型和宏，用于处理程序执行期间可能出现的各种条件；
12. <setjmp.h>：包含可以绕过一般函数调用并返回序列的函数的原型，即非局部跳转；
13. <locale.h>：包含函数原型和其他信息，使程序可以针对所运行的地区进行修改。地区的表示方法可以使计算机系统处理不同的数据表达约定，如全世界的日期、时间、美元数和大数字；
14. <assert.h>：包含宏和信息，用于进行诊断，帮助程序调试。
15. <limits.h>：定义了各种数据类型的极限值。这些极限值是通过宏定义的，它们为整数类型（如char、short、int、long和long long）以及其他数据类型提供了最大值和最小值的信息。

上述库函数在其各种支持 C 语言的 IDE 中都是有的。  

GNU C 函数库是一种类似于第三方插件的东西。由于 Linux 是用 C 语言写的，所以 Linux 的一些操作是用 C 语言实现的，因此，GUN 组织开发了一个 C 语言的库   以便让我们更好的利用 C 语言开发基于 Linux 操作系统的程序。 不过现在的不同的 Linux 的发行版本对这两个函数库有不同的处理方法，有的可能已经集成在同一个库里了。  


glibc是linux下面c标准库的实现，即GNU C Library。glibc本身是GNU旗下的C标准库，后来逐渐成为了Linux的标准c库，而Linux下原来的标准c库Linux libc逐渐不再被维护。Linux下面的标准c库不仅有这一个，如uclibc、klibc，以及上面被提到的Linux libc，但是glibc无疑是用得最多的。glibc在/lib目录下的.so文件为libc.so.6。

