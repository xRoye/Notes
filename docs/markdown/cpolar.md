# cpolar 内网穿透

https://www.cpolar.com/  
https://dashboard.cpolar.com/status  
https://www.cpolar.com/docs  

## Docker 部署

```
docker search cpolar #查找安装
docker pull probezy/cpolar

docker run -id --network host --name cpolar probezy/cpolar #启动

docker exec -it cpolar /bin/bash #进容器bash

cpolar authtoken 复制的token #进官网登陆，左侧有验证选项，复制sh也行

cpolar http 8081 # 穿透8081端口
cpolar http 8899
cpolar http 9200

cpolar tcp 22 # tcp穿透22端口
```

### 启动指令分解

-i 以交互模式运行容器  
-d: 后台运行容器  

docker网络
当你安装完Docker时，它会自动创建三个网络。你可以使用以下docker network ls命令列出这些网络：

docker network ls
结果应如下

NETWORK ID          NAME                DRIVER              SCOPE
594430d2d4bb        bridge              bridge              local
d855b34c5d51        host                host                local
b1ecee29ed5e        none                null                local
Docker内置这三个网络，运行容器时，你可以使用该来指定容器应连接到哪些网络。

我们在使用docker run创建Docker容器时，可以用--network标志 选项指定容器的网络模式，Docker有以下4种网络模式：

host模式：使用 --net=host 指定。

none模式：使用 --net=none 指定。

bridge模式：使用 --net=bridge 指定，默认设置。

container模式：使用 --net=container:NAME_or_ID 指定。