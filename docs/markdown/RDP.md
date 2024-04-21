# 适用于 Windows 家庭版 开启远程桌面 Remote Desktop Protocol

## 防火墙 —— 入站规则
https://zhuanlan.zhihu.com/p/600706130


一、增加入站规则
1、在Win11任务栏搜索栏中输入“高级安全”，打开“高级安全 Windows Defender 防火墙”菜单

2、点击左侧“入站规则”，再点击右侧“新建规则”

3、新入站规则的具体设置

`端口——TCP——3389——允许`

## 使用 RDPWrap 套件 开启RDP
https://zhuanlan.zhihu.com/p/445216327
二、使用 RDPWrap 套件进行激活 RDP Wrapper Library v1.6.2
https://github.com/stascorp/rdpwrap/releases

1、、install.bat 使用管理员身份运行

出现如图的 “RDP Wrapper Library is already installed”字样即安装成功。

2、update.bat 使用管理员身份运行
这个用来更新的批处理文件是要向GitHub发送请求的，由于懂得都懂的原因，更新不能，症状表现与解决方式在下面有写

3、RDPConf.exe 使用管理员身份运行

图中几处绿色的地方，如果都绿了，说明已经设置成功。有红色不行

4、右键以管理员身份运行RDPCheck.exe，出现远程桌面登录界面则表示安装正常。

二、问题以及解决方法
1、运行RDPConf.exeWrapped state和Service state都正常，但是Listener State显示not Listening 【not supported】
C:\Program Files\RDP Wrapper\rdpwrap.ini 里面有个链接，手动打开链接，copy内容到该文件中，保存后，手动重启服务
```
net stop termservice
net start termservice
```

## RDP 微软账号登录问题
RDP 微软账号登录问题 https://zhuanlan.zhihu.com/p/600623397

设置 - 账户 - 登陆选项 - 为了提高安全性，仅允许对此设备上的Microsoft账户使用Windows Hello登录  把这个关闭 
然后就能使用微软账号和密码登录了（微软账号的密码，不是pin）

## ipv6可用
ref. https://zhuanlan.zhihu.com/p/627542501

1. 先保证ipv4下RDP可用
2. 路由器开启ipv6功能
3. CMD `ipconfig` 查看 临时ipv6地址，就是这个地址应该可以连RDP了
4. 或者去这个网站 https://ipw.cn/ 查一下是不是全绿且ipv6优先，而且ipv6地址应该和上一步是一样的