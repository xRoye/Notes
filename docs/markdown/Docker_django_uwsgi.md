# Docker部署Django 之 单容器部署Django + Uwsgi

Ref. [Docker部署Django由浅入深系列(上)：单容器部署Django + Uwsgi](https://zhuanlan.zhihu.com/p/141976805)

Ps. 这个博主(大江狗)写了很多python web，django 相关的文章，很有参考价值，[大江狗的博客](https://pythondjango.cn/)

参考连接内写的真的非常详细，这里直入主题做一下核心步骤的摘录，并做适当的补充

## 关于 Django、Uwsgi、Nginx

Ref. [Django + Uwsgi + Nginx 的生产环境部署](https://www.cnblogs.com/yangmaosen/p/12409974.html)

Django、Uwsgi、Nginx相关基础知识

## 注意点

1. 本例的数据库用的内置sqlite，使用其他数据库需要自行修改

2. Django默认ALLOWED_HOSTS = []为空，在正式部署前你需要修改settings.py, 把它设置为服务器实际对外IP地址，否则后面部署会出现错误，这个与docker无关。即使你不用docker部署，ALLOWED_HOSTS也要设置好的。

## 构建环境准备
项目结构如下(mysite2为例)
```
 mysite2
 ├── db.sqlite3
 ├── Dockerfile # 构建docker镜像所用到的文件
 ├── manage.py
 ├── mysite2
 │   ├── asgi.py
 │   ├── __init__.py
 │   ├── settings.py
 │   ├── urls.py
 │   └── wsgi.py
 ├── pip.conf
 ├── requirements.txt # 两个依赖：django==3.0.5 uwsgi==2.0.18
 ├── start.sh # 进入容器后需要执行的命令，后面会用到
 └── uwsgi.ini # uwsgi配置文件
```

Dockerfile内容如下

```
 # 建立 python3.7 环境
 FROM python:3.7
 
 # 镜像作者大江狗
 MAINTAINER DJG
 
 # 设置 python 环境变量
 ENV PYTHONUNBUFFERED 1
 
 # 设置pypi源头为国内源
 COPY pip.conf /root/.pip/pip.conf
 
 # 在容器内/var/www/html/下创建 mysite2 文件夹
 RUN mkdir -p /var/www/html/mysite2
 
 # 设置容器内工作目录
 WORKDIR /var/www/html/mysite2
 
 # 将当前目录文件拷贝一份到工作目录中（. 表示当前目录）
 ADD . /var/www/html/mysite2
 
 # 利用 pip 安装依赖
 RUN pip install -r requirements.txt
 
 # Windows环境下编写的start.sh每行命令结尾有多余的\r字符，需移除。
 RUN sed -i 's/\r//' ./start.sh
 
 # 设置start.sh文件可执行权限
 RUN chmod +x ./start.sh
```

start.sh脚本文件内容如下所示。最重要的是最后一句，使用uwsgi.ini配置文件启动Django服务。

```
 #!/bin/bash
 # 从第一行到最后一行分别表示：
 # 1. 生成数据库迁移文件
 # 2. 根据数据库迁移文件来修改数据库
 # 3. 用 uwsgi启动 django 服务, 不再使用python manage.py runserver
 python manage.py makemigrations&&
 python manage.py migrate&&
 uwsgi --ini /var/www/html/mysite2/uwsgi.ini
 # python manage.py runserver 0.0.0.0:8000
```


uwsgi.ni配置文件内容如下

```
 [uwsgi]
 project=mysite2
 uid=www-data
 gid=www-data
 base=/var/www/html
 
 chdir=%(base)/%(project)
 module=%(project).wsgi:application
 master=True
 processes=2
 
 http=0.0.0.0:8000 #这里直接使用uwsgi做web服务器，使用http。如果使用nginx，需要使用socket沟通。
 buffer-size=65536
 
 pidfile=/tmp/%(project)-master.pid
 vacuum=True
 max-requests=5000
 daemonize=/tmp/%(project)-uwsgi.log
 
 #设置一个请求的超时时间(秒)，如果一个请求超过了这个时间，则请求被丢弃
 harakiri=60
 #当一个请求被harakiri杀掉会，会输出一条日志
 harakiri-verbose=true
```


另有 pip源参考配置如下：
```
pip源设置成了阿里云镜像，pip.conf文件内容如下所示：

 [global]
 index-url = https://mirrors.aliyun.com/pypi/simple/
 [install]
 trusted-host=mirrors.aliyun.com
```

## 构建环境

单容器部署 Django + UWSGI

1. 生成名为django_uwsgi_img的镜像 注意 在工程目录下(有Dockerfile的目录下)执行，注意最后的 "." 别丢了  
    `sudo docker build -t django_uwsgi_img .`

2. 启动并运行mysite2的容器  宿主机80：容器8000。-d表示后台运行  
    `docker run -it -d --name django-test -p 80:8000 django_uwsgi_img`  
    指定网卡启动  
    `docker run -it -d --name django --network my-net -p 80:8000 django_uwsgi_img`  
    关于网卡配置参考 [Docker-网络配置](/markdown/Docker.md#docker-网络配置)

3. 进入mysite2的容器内部，并运行脚本命令start.sh  
    进入容器，如果复制命令的话，结尾千万不能有空格。  
    `sudo docker exec -it mysite2 /bin/bash`  
    执行脚本命令  
    `sh start.sh`  
    以上两句命令也可以合并成一条命令  
    `sudo docker exec -it mysite2 /bin/bash start.sh`

执行后效果如下所示。当你看到最后一句\[uWSGI\]时，说明uwsgi配置并启动完成。


这时你打开浏览器输入http://your_server_ip，你就可以看到你的Django网站已经上线了

恭喜你！这次是uwsgi启动的服务哦，因为你根本没输入python manage.py runserver命令。


其实上述过程可以配置成docker启动执行，摘录如下

### docker容器启动时运行脚本

首先要写个一个开机脚本，脚本内容是你想要的程序。然后将其保存在根目录或者任意目录下。然后在run容器时，加上该脚本，这样每次容器启动都会运行该脚本。命令如下：

`docker run  -itd --name test --restart=always amd64/ubuntu:18.04 /bin/bash /myStart.sh`

需要注意的是，脚本（如myStart.sh）必须写绝对路径，而且前面必须有/bin/bash，该语句的意思就是启动容器时，使用/bin/bash来运行/myStart.sh这个脚本。

有些时候，如果我们需要使用多个脚本，可以使用一个脚本来启动其它的脚本，也可以使用下列命令

docker run  -itd --name test --restart=always amd64/ubuntu:18.04 /bin/bash /1.sh;/2.sh;/3.sh

————————————————
版权声明：本文为博主原创文章，遵循 CC 4.0 BY-SA 版权协议，转载请附上原文出处链接和本声明。  
原文链接：https://blog.csdn.net/weixin_38693938/article/details/115214293



### 故障排查：

此时如果你没有看到网站上线，主要有两个可能原因：

1. uwsgi配置文件错误。  
尤其http服务IP地址为0.0.0.0:8000，不应是服务器的ip:8000，因为我们uwsgi在容器里，并不在服务器上。  
参见  [IP 0.0.0.0 与 localhost](/markdown/ip.md#localhost127001和0000和本机ip的区别)
2. 浏览器设置了http(端口80)到https(端口443)自动跳转。  
因为容器8000端口映射的是宿主机80端口，如果请求来自宿主机的443端口，容器将接收不到外部请求。  
解决方案清空浏览器设置缓存或换个浏览器。

3. 访问数据库异常。  
settings.py 中 数据库配置HOST 如果数据库不在同一个容器中，不能使用 localhost，可根据数据库容器ip配置，也可用主机的ip地址，总之注意是HOST的问题
但是这样相当不优雅，因为ip会变，可以通过创建自定义网络，并直接通过容器名称访问 ~~【并在容器启动时指定ip实现（docker默认网络不能指定ip）这个比起名称还是略显麻烦了】~~   
参见  [Docker-网络配置](/markdown/Docker.md#docker-网络配置)

注意：你会留意到网站虽然上线了，但有些图片和网页样式显示不对，这是因为uwsgi是处理动态请求的服务器，静态文件请求需要交给更专业的服务器比如Nginx处理。

~~下篇文章中我们将介绍Docker双容器部署Django+Uwsgi+Nginx，欢迎关注。~~

再进阶直接看最终篇把  

[Docker完美部署Django Uwsgi+Nginx+MySQL+Redis](https://zhuanlan.zhihu.com/p/145364353)

[Docker部署Django](https://pythondjango.cn/django/advanced/16-docker-deployment/)
