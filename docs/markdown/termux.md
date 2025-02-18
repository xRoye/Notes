# Termux

#### 安装
https://github.com/termux/termux-app/releases

修改源  
`termux-change-repo`  
`apt update`
`apt upgrade`

从屏幕左侧向内滑动可显示伸缩导航条（如果启用了安卓系统中的手势导航就需要在屏幕边缘短暂按住一小会再滑动以打开导航条）

### 安装 linux

先安装管理器  
`pkg install proot-distro`

列出可用系统  
`proot-distro list`

安装指定系统 以alpine为例  
`proot-distro install alpine`

登录指定系统 以alpine为例  
`proot-distro login alpine`

### 安装 code-server

https://github.com/coder/code-server

官方提供了termux下的安装方式：  
https://coder.com/docs/code-server/termux#install  
```
Run `pkg install tur-repo`
Run `pkg install code-server`
You can now start code server by simply running `code-server`.
```

登录地址  
`127.0.0.1:8080`  
密码被记录在  
`/data/data/com.termux/files/home/.config/code-server/config.yaml`   
初始密码很复杂，修改密码重启server比较好把  
`nano /data/data/com.termux/files/home/.config/code-server/config.yaml`  


### Android 下的pc模拟器
ref. https://www.bilibili.com/opus/824360204451708945
