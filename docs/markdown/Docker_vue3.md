# Docker 打包 vue3 项目

Ref. https://blog.csdn.net/weixin_57849570/article/details/132101497

## vue项目build

### 执行前的提示：务必使工程内部没有无关的文件，以及各种其实不执行的测试文件，打包过程会校验所有文件

一般情况下 工程目录执行 `npm run build`

实际运行的指令其实写在 工程目录 package.json 的 scripts 组中

eg:
```
"scripts": {
    "preinstall": "npx only-allow pnpm",
    "dev": "vite serve --mode development",
    "build" : "vite build",
    "build:prod": "vite build --mode production && vue-tsc --noEmit",
    "prepare": "husky install",
    "lint:eslint": "eslint  --fix --ext .ts,.js,.vue ./src ",
    "lint:prettier": "prettier --write \"**/*.{js,cjs,ts,json,tsx,css,less,scss,vue,html,md}\"",
    "lint:stylelint": "stylelint  \"**/*.{css,scss,vue}\" --fix",
    "lint:lint-staged": "lint-staged",
    "commit": "git-cz"
  },
```



```
$$$>npm run build

> vue3-element-admin@2.6.2 build
> vite build

vite v4.4.10 building for production...
transforming (1169) node_modules\element-plus\es\components\select\style\css.mjs[@vue/compiler-sfc] ::v-deep usage as a combinator has been deprecated. Use :deep(<inner-selector>) instead.

✓ 2609 modules transformed.
dist/assets/chart-a027cd52.svg                                                               0.18 kB │ gzip:   0.15 kB
dist/assets/size-fb05acae.svg                                                                0.21 kB │ gzip:   0.18 kB
dist/assets/close_all-4600fcf5.svg                                                           0.28 kB │ gzip:   0.22 kB
dist/assets/close_other-613113f5.svg                                                         0.28 kB │ gzip:   0.23 kB
dist/assets/guide-441e177e.svg                                                               0.32 kB │ gzip:   0.24 kB

......


✓ built in 31.39s

```

打包成功的内容在工程目录的 `dist` 文件夹中

## nginx 镜像

先 `docker pull nginx`
拉个nginx的镜像，在这个基础上部署项目

然后创建个文件夹，准备包装项目  
比如起名为 Docker_vue  
下面均以此文件夹为docker项目目录

把上一步生成的dist文件夹 copy到Docker_vue一份

目录结构如下

```
Docker_vue
│  default.conf
│  Dockerfile
│
└─dist
```
------------------------------


### 配置文件

配置文件 在 Docker_vue 下创建一个 文件 default.conf

docker文件 在 Docker_vue 下创建一个 文件 Dockerfile  
注意：Dockerfile没有后缀名

-------------------

default.conf ---- nginx的配置文件，这里只是一个例子

更详细的nginx配置值得单列一个主题编写

```
server {
    listen       80;
    server_name  localhost; # 修改为docker服务宿主机的ip
 
    location / {
        root   /usr/share/nginx/html;
        index  index.html index.htm;
        try_files $uri $uri/ /index.html =404;
    }
 
    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   html;
    }
}
```

Dockerfile

```
FROM nginx
 
#MAINTAINER Roye
LABEL org.opencontainers.image.authors="Roye@abc.def"
 
RUN rm /etc/nginx/conf.d/default.conf
 
ADD default.conf /etc/nginx/conf.d/
 
COPY dist/ /usr/share/nginx/html/
```

解释一个
```
FROM nginx：该镜像是基于nginx:latest镜像构建的
 
#MAINTAINER Roye：添加作者说明 已经过时了现在用LABEL
#参考 https://docs.docker.com/reference/dockerfile/#maintainer
LABEL org.opencontainers.image.authors="Roye@abc.def"
 
RUN rm /etc/nginx/conf.d/default.conf：删除目录下的default.conf文件
 
ADD default.conf /etc/nginx/conf.d/：将default.conf复制到/etc/nginx/conf.d/下，用本地的default.conf配置来替换nginx镜像里的默认配置
 
COPY dist/ /usr/share/nginx/html/：将项目根目录下dist文件夹（构建之后才会生成）下的所有文件复制到镜像/usr/share/nginx/html/目录下
```
### 打包镜像

`docker build -t [镜像名称] .`

eg  
`docker build -t vue_docker_demo .`

注意事项：-t 后输入给镜像取的名称，最后的点 （.） 不要忘记，代表给利用当前 dockerfile 构建镜像

### 启动

内部80端口 映射 外部9090

`docker run --name [容器名] -p 9090:80 -d [镜像名]`

`docker run --name my_docker_vue3 -p 9090:80 -d vue_docker_demo:latest`

docker run：基于镜像启动一个容器
 
-d：后台方式启动
 
-p 9090:80: 端口映射，将宿主机的9090端口映射到容器的80端口
 
--name：容器名，我起的叫test
 
xxxx：要启动的镜像名称
