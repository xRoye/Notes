# Zerotier

## 常用指令

`zerotier-cli info` 连接状态  
`zerotier-cli peers` 节点信息  

## Windows 下 搭建 moon 节点

ref. https://www.cnblogs.com/dqi1999/p/13740130.html  
ref. https://liaoguoyin.com/posts/zerotier-moon/

### 前言
所有zerotier命令的实际执行，在windows中实际上是位于`C:\ProgramData\ZeroTier\One\`目录下的`zerotier-one_x64.exe` ，只是用了不同参数

在ui目录`C:\Program Files (x86)\ZeroTier\One`中，有一个`zerotier-idtool.bat`  
内容为
```
@ECHO OFF
if [C:\ProgramData\ZeroTier\One\zerotier-one_x64.exe] == [] (
	 -i %*
) else (
	C:\ProgramData\ZeroTier\One\zerotier-one_x64.exe -i %*
)
```
`zerotier-cli`也是一样的
```
@ECHO OFF
if [C:\ProgramData\ZeroTier\One\zerotier-one_x64.exe] == [] (
	 -q %*
) else (
	C:\ProgramData\ZeroTier\One\zerotier-one_x64.exe -q %*
)
```

### moon配置生成
为了方便，直接去`zerotier-one_x64.exe`目录下用  
`cd C:\ProgramData\ZeroTier\One`
`zerotier-one_x64.exe -i initmoon identity.public >>moon.json`

### 配置编辑
原博主：
`"stableEndpoints": [ "8.8.8.8/9993" ] #8.8.8.8 改为你的公网IP`

这里我用cpolar做的ddns，ip尝试改为获取的url  
`"stableEndpoints": ["33.tcp.cpolar.top/11730"]`

#### 端口转发
这里因为是ddns是随机端口，在ddns配置的时候用了指定的端口地址9993  
所以实际上会有问题  

在windows下尝试使用netsh做个端口转发  
ref. https://blog.csdn.net/bzjoko/article/details/128786478  

`netsh interface portproxy add v4tov4 listenaddress=127.0.0.1 listenport=9993 connectaddress=127.0.0.1 connectport=11730`增加端口配置
`netsh interface portproxy show all` 查看已有端口配置

删除是`netsh interface portproxy delete v4tov4 listenaddress=127.0.0.1 listenport=9993`


### 生成MOON签名
`zerotier-one_x64.exe -i genmoon moon.json`

运行后在C:\ProgramData\ZeroTier\One\目录下生成类似000000xxxxx.moon的文件
，.moon前面的即这台MOON的ID，后续使用时需要，注意保存

### 把MOON加入网络中
运行后在C:\ProgramData\ZeroTier\One\目录下建立文件夹 moons.d，将刚刚生成的 .moon 文件拷贝进去。

### 重启 ZeroTier
服务 ZeroTier one 重启启动

### 其他机器添加moon

```
不同客户端版本 moon-id 不一致，需要检查本机 zerotier 版本（zerotier-cli -v 命令可查看版本），如：

在 zerotier 1.10.6 版本中， moon-id 是 16 位，前面有多余 0
在 zerotier 1.12.2 版本后，moon-id 是 10 位字符串
总的来说，在新版中 moon-id 是 10 位字符串，需要注意删除前面多余的 0

这是一个很奇怪的更新，在官方的文档中我没找到任何明确的说明，还是自己去 Github 开 Issues 问到的（笑脸黄豆.jpg）
```
`zerotier-cli orbit xxxxx xxxxx  #moon服务器的ID值，输入2遍`  
`zerotier-cli listmoons` 中有内容，才算是添加成功

结果，失败了  
根据ref. https://blog.csdn.net/bzjoko/article/details/128786478   
端口使用UDP  
但是  cpolar只能配置成tcp






