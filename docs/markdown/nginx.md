# Nginx

## About
Nginx (engine x) 是一个高性能的HTTP和反向代理web服务器，  
Nginx是lgor Sysoev为俄罗斯访问量第二的rambler.ru站点设计开发的。  
Nginx是一款轻量级的Web 服务器/反向代理服务器及电子邮件（IMAP/POP3）代理服务器，在BSD-like 协议下发行。其特点是占有内存少，并发能力强，事实上nginx的并发能力确实在同类型的网页服务器中表现较好，中国大陆使用nginx网站用户有：百度、京东、新浪、网易、腾讯、淘宝等。

相关：Apache Lighttpd tomcat

## 关于正向代理与反向代理

反向代理：

反向代理（Reverse Proxy）方式是指以代理服务器来接受internet上的连接请求，然后将请求转发给内部网络上的服务器，并将从服务器上得到的结果返回给internet上请求连接的客户端，此时代理服务器对外就表现为一个反向代理服务器。

正向代理:

是一个位于客户端和原始服务器(origin server)之间的服务器，为了从原始服务器取得内容，客户端向代理发送一个请求并指定目标(原始服务器)，然后代理向原始服务器转交请求并将获得的内容返回给客户端。客户端才能使用正向代理。



正向代理和反向代理区别？

正向代理，是在客户端的。比如需要访问某些国外网站，我们可能需要购买vpn。并且vpn是在我们的用户浏览器端设置的(并不是在远端的服务器设置)。浏览器先访问vpn地址，vpn地址转发请求，并最后将请求结果原路返回来。  
反向代理是作用在服务器端的，是一个虚拟ip(VIP)。对于用户的一个请求，会转发到多个后端处理器中的一台来处理该具体请求。


## nginx典型目录结构

```
├── conf                                              这是nginx所有配置文件的目录
│   ├── fastcgi.conf                                      fastcgi 相关参数的配置文件
│   ├── fastcgi.conf.default                          fastcgi.conf 的原始备份
│   ├── fastcgi_params                                fastcgi的参数文件
│   ├── fastcgi_params.default
│   ├── koi-utf
│   ├── koi-win
│   ├── mime.types                                    媒体类型
│   ├── mime.types.default
│   ├── nginx.conf                                    nginx默认的主配置文件
│   ├── nginx.conf.default
│   ├── scgi_params                                scgi 相关参数文件
│   ├── scgi_params.default
│   ├── uwsgi_params                            uwsgi相关参数文件
│   ├── uwsgi_params.default
│   └── win-utf
​
├── fastcgi_temp                                 fastcgi临时数据目录
​
├── html                                              这是编译安装时nginx的默认站点目录，类似Apache的默认站点htdocs  
│   ├── 50x.html                                  错误页面优雅替代显示文件，例如：出现502错误时会调用此页面error_page 500 502 503 504 /50x.html
│   └── index.html                               默认的首页文件，index.html\index.asp\index.jsp来做网站的首页文件
​
├── logs                                              nginx默认的日志路径，包括错误日志及访问日志
│   ├── access.log                               nginx的默认访问日志文件，使用tail -f access.log,可以实时观看网站的用户访问情况信息
│   ├── error.log                                 nginx的错误日志文件，如果nginx出现启动故障可以查看此文件
│   └── nginx.pid                           
```


## 常用命令

```
start nginx   启动nginx
nginx -s stop 快速关闭Nginx，可能不保存相关信息，并迅速终止web服务。 
nginx -s quit 平稳关闭Nginx，保存相关信息，有安排的结束web服务。 
nginx -s reload 因改变了Nginx相关配置，需要重新加载配置而重载。 
nginx -s reopen 重新打开日志文件。
nginx -c filename 为 Nginx 指定一个配置文件，来代替缺省的。 
nginx -t 不运行，而仅仅测试配置文件。nginx 将检查配置文件的语法的正确性，并尝试打开配置文件中所引用到的文件。 
nginx -v 显示 nginx 的版本。 
nginx -V 显示 nginx 的版本，编译器版本和配置参数
```

windows下 需要带上exe `./nginx.exe -s stop`


## 配置文件

### nginx.conf
/etc/nginx/nginx.conf

```
#user  nobody;
worker_processes  1;

#error_log  logs/error.log;
#error_log  logs/error.log  notice;
#error_log  logs/error.log  info;

#pid        logs/nginx.pid;


events {
    worker_connections  1024;
}


http {
    include       mime.types;
    default_type  application/octet-stream;

    #log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
    #                  '$status $body_bytes_sent "$http_referer" '
    #                  '"$http_user_agent" "$http_x_forwarded_for"';

    #access_log  logs/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    #keepalive_timeout  0;
    keepalive_timeout  65;

    #gzip  on;

    server {
        listen       80;
        server_name  localhost;

        #charset koi8-r;

        #access_log  logs/host.access.log  main;

        location / {
            root   html;
            index  index.html index.htm;
        }

        #error_page  404              /404.html;

        # redirect server error pages to the static page /50x.html
        #
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }

        # proxy the PHP scripts to Apache listening on 127.0.0.1:80
        #
        #location ~ \.php$ {
        #    proxy_pass   http://127.0.0.1;
        #}

        # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
        #
        #location ~ \.php$ {
        #    root           html;
        #    fastcgi_pass   127.0.0.1:9000;
        #    fastcgi_index  index.php;
        #    fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;
        #    include        fastcgi_params;
        #}

        # deny access to .htaccess files, if Apache's document root
        # concurs with nginx's one
        #
        #location ~ /\.ht {
        #    deny  all;
        #}
    }


    # another virtual host using mix of IP-, name-, and port-based configuration
    #
    #server {
    #    listen       8000;
    #    listen       somename:8080;
    #    server_name  somename  alias  another.alias;

    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}


    # HTTPS server
    #
    #server {
    #    listen       443 ssl;
    #    server_name  localhost;

    #    ssl_certificate      cert.pem;
    #    ssl_certificate_key  cert.key;

    #    ssl_session_cache    shared:SSL:1m;
    #    ssl_session_timeout  5m;

    #    ssl_ciphers  HIGH:!aNULL:!MD5;
    #    ssl_prefer_server_ciphers  on;

    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}

}
```


抽象结构

```
...              #全局块

events {         #events块
   ...
}

http      #http块
{
    ...   #http全局块
    server        #server块
    { 
        ...       #server全局块
        location [PATTERN]   #location块
        {
            ...
        }
        location [PATTERN] 
        {
            ...
        }
    }
    server
    {
      ...
    }
    ...     #http全局块
}
```

+ 1、全局块：配置影响nginx全局的指令。一般有运行nginx服务器的用户组，nginx进程pid存放路径，日志存放路径，配置文件引入，允许生成worker process数等。
+ 2、events块：配置影响nginx服务器或与用户的网络连接。有每个进程的最大连接数，选取哪种事件驱动模型处理连接请求，是否允许同时接受多个网路连接，开启多个网络连接序列化等。
+ 3、http块：可以嵌套多个server，配置代理，缓存，日志定义等绝大多数功能和第三方模块的配置。如文件引入，mime-type定义，日志自定义，是否使用sendfile传输文件，连接超时时间，单连接请求数等。
+ 4、server块：配置虚拟主机的相关参数，一个http中可以有多个server。
+ 5、location块：配置请求的路由，以及各种页面的处理情况。

eg

```
########### 每个指令必须有分号结束。#################
#user administrator administrators;  #配置用户或者组，默认为nobody nobody。
#worker_processes 2;  #允许生成的进程数，默认为1
#pid /nginx/pid/nginx.pid;   #指定nginx进程运行文件存放地址
error_log log/error.log debug;  #制定日志路径，级别。这个设置可以放入全局块，http块，server块，级别以此为：debug|info|notice|warn|error|crit|alert|emerg
events {
    accept_mutex on;   #设置网路连接序列化，防止惊群现象发生，默认为on
    multi_accept on;  #设置一个进程是否同时接受多个网络连接，默认为off
    #use epoll;      #事件驱动模型，select|poll|kqueue|epoll|resig|/dev/poll|eventport
    worker_connections  1024;    #最大连接数，默认为512
}
http {
    include       mime.types;   #文件扩展名与文件类型映射表
    default_type  application/octet-stream; #默认文件类型，默认为text/plain
    #access_log off; #取消服务日志    
    log_format myFormat '$remote_addr–$remote_user [$time_local] $request $status $body_bytes_sent $http_referer $http_user_agent $http_x_forwarded_for'; #自定义格式
    access_log log/access.log myFormat;  #combined为日志格式的默认值
    sendfile on;   #允许sendfile方式传输文件，默认为off，可以在http块，server块，location块。
    sendfile_max_chunk 100k;  #每个进程每次调用传输数量不能大于设定的值，默认为0，即不设上限。
    keepalive_timeout 65;  #连接超时时间，默认为75s，可以在http，server，location块。

    upstream mysvr {   
      server 127.0.0.1:7878;
      server 192.168.10.121:3333 backup;  #热备
    }
    error_page 404 https://www.baidu.com; #错误页
    server {
        keepalive_requests 120; #单连接请求上限次数。
        listen       4545;   #监听端口
        server_name  127.0.0.1;   #监听地址       
        location  ~*^.+$ {       #请求的url过滤，正则匹配，~为区分大小写，~*为不区分大小写。
           #root path;  #根目录
           #index vv.txt;  #设置默认页
           proxy_pass  http://mysvr;  #请求转向mysvr 定义的服务器列表
           deny 127.0.0.1;  #拒绝的ip
           allow 172.18.5.54; #允许的ip           
        } 
    }
}
```
需要注意的有以下几点：

1、几个常见配置项：

+ 1.$remote_addr 与 $http_x_forwarded_for 用以记录客户端的ip地址；
+ 2.$remote_user ：用来记录客户端用户名称；
+ 3.$time_local ： 用来记录访问时间与时区；
+ 4.$request ： 用来记录请求的url与http协议；
+ 5.$status ： 用来记录请求状态；成功是200；
+ 6.$body_bytes_s ent ：记录发送给客户端文件主体内容大小；
+ 7.$http_referer ：用来记录从那个页面链接访问过来的；
+ 8.$http_user_agent ：记录客户端浏览器的相关信息；

2、惊群现象：一个网路连接到来，多个睡眠的进程被同时叫醒，但只有一个进程能获得链接，这样会影响系统性能。

3、每个指令必须有分号结束。


nginx.conf文件里一般有 include /etc/nginx/conf.d/*.conf，所以conf.d文件夹下的 .conf配置也会起作用。

两者都是nginx的配置文件，nginx.conf为主配置。

### default.conf
/etc/nginx/conf.d/default.conf



## location匹配规则

ref. https://zhuanlan.zhihu.com/p/459730965

网站上用nginx非常广泛, 但是它的配置文件比较复杂, 这里讨论下nginx的location匹配规则.

> 约定熟成: []表示里面的参数能省略, <>表示里面的参数不能省略.

### location的两种语法

第一种语法分为3个部分, 分别是: `location关键字`+`@name别名(name是自己取的名字)`+`如何处理`, 这个语法很简单, 就是做内部跳转, 这里不讨论了.
> location @name { ... }


第二种语法分为4个部分, 分别是: `location关键字` + `匹配方式符号(可省略)`+`匹配规则`+`如何处理`, 这个最复杂也是最常用, 我们只讨论这个.

> location [ = | ~ | ~* | ^~ ] uri { ... }


### 普通匹配和正则匹配

这个语法的难点全部集中在`[ = | ~ | ~* | ^~ ]`这里, 只要搞懂这个就能正确使用location.

`[ = | ~ | ~* | ^~ ]`分为两种匹配模式, 分别是普通匹配和正则匹配.

### 普通匹配概述

> `=` : 这代表精准匹配全路径, 命中它后直接返回, 不再进行后续匹配, 优先级最高.
> 
> `^~` :  这代表精准匹配开头, 命中开头后直接返回, 不再进行后续匹配, 优先级第二.
> 
> `无匹配方式符号` : 这代表通用性匹配, 命中后还会继续后续匹配, 最后选取路径最长的匹配, 并储存起来, 优先级第四.

#### 普通匹配举例

```
#这是精准匹配, 只有请求路径完全匹配`/index.html`才会命中它
location = /index.html {
  ...
}
#这是精准匹配开头, 只要请求路径的开头是`/image/`, 就会命中并立即返回
location ^~ /image/ {
  ...
}
#这是无匹配方式符号的普通匹配, 如果请求路径开头是`/image/`, 则会命中, 但是不会立即返回还会接着进行普通匹配
location /image/ {
  ...
}
#这是无匹配方式符号的普通匹配, 如果请求路径开头是`/image/meinv`, 则会命中, 但是不会立即返回还会接着进行普通匹配, 同时会舍弃掉上面那个匹配
location /image/meinv {
  ...
}
```

### 正则匹配概述

> `~`: 这是区分大小写的正则匹配, 命中后则不进行后续匹配, 立即返回, 优先级第三.
> 
> `*~`: 不区分大小写的正则匹配, 命中后则不进行后续匹配, 立即返回, 优先级第三.

这里有个很重要点点, 也就是正则匹配中`~`和`*~`优先级一样, 它们按照从上到下的顺序进行匹配, 最先命中的立即返回, 后续的不会进行匹配, 所以**精细的正则匹配规则往前放, 通用的正则匹配规则往后放**.

#### 正则匹配举例

```
#区分大小写的正则匹配, 如果路径包含 /image/ 则立即返回, 注意这里并不需要开头命中, 因为这是正则表达式
location ~ /image/ {
  ...
}

#区分大小写的正则匹配, 如果路径包含 /image(不分大小写)/ 则立即返回, 注意这里并不需要开头命中, 因为这是正则表达式, 但由于上面一个正则匹配规则在前面, 所以如果路径包含 /image/ 则会被挡下来, 匹配不到这里.
location *~ /IMAGE/ {
  ... 
}
```

### location如何匹配?

1. 先进行普通匹配中的`精准匹配`, 如果命中了立马返回.

2. 然后进行普通匹配中的`精准开头匹配`, 如果命中则立马返回.

3. 进行普通匹配中的 `无匹配符号` 匹配, 如果命中继续匹配, 知道普通匹配全部完成, 并保存路径最长的匹配.

4. 由上自下进行正则匹配, 如果命中立即返回.

5. 如果正则匹配全部失败, 则返回普通匹配中存放的匹配.
