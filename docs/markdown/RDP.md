# 适用于 Windows 家庭版 开启远程桌面 Remote Desktop Protocol

## 前排提醒
当前 Android移动端 RD客户端 在连接的时候不要点 “不再询问” 字样的复选框  
否则会在第二次连接的时候一直转圈进不去的bug，（需要清数据）

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
C:\Program Files\RDP Wrapper\rdpwrap.ini 里面有个链接，手动打开链接，copy内容到该文件中，  
没有的话，在这里：https://raw.gitmirror.com/sebaxakerhtc/rdpwrap.ini/master/rdpwrap.ini  
或者访问这位作者的github看看：https://github.com/sebaxakerhtc/rdpwrap.ini  
保存后，手动重启服务  
```
net stop termservice
net start termservice
```

## RDP 微软账号登录问题
RDP 微软账号登录问题 https://zhuanlan.zhihu.com/p/600623397

设置 - 账户 - 登陆选项 - 为了提高安全性，仅允许对此设备上的Microsoft账户使用Windows Hello登录  把这个关闭 
然后就能使用微软账号和密码登录了（微软账号的密码，不是pin）

## ipv6 概率可用
ref. https://zhuanlan.zhihu.com/p/627542501

1. 先保证ipv4下RDP可用
2. 路由器开启ipv6功能
3. CMD `ipconfig` 查看 临时ipv6地址，就是这个地址应该可以连RDP了
4. 或者去这个网站 https://ipw.cn/ 查一下是不是全绿且ipv6优先，而且ipv6地址应该和上一步是一样的

## 提升远程桌面帧率
ref. https://learn.microsoft.com/zh-cn/troubleshoot/windows-server/remote/frame-rate-limited-to-30-fps

远程帧率的桌面默认限制在30帧，可以通过注册表修改提升到60FPS，大幅提升流畅度

注意，经过实测官网的说法并不靠谱= =

1. 通过快捷键WIN+R打开运行窗口，输入regedit，打开注册表编辑器
2. 找到HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations
3. 右键新建，选择DWORD(32-bit),输入文件名:DWMFRAMEINTERVAL
4. 输入 十进制 16 （这里和官方给的说法完全不一样= =）
    + 实际上写的就是帧生成时间，在testufo里能看到帧生成时间和在这写的保持一致，1000ms/帧率 = 帧生成时间， 1000ms/帧生成时间 = 帧率，
    + 60fps--16.66ms 实测16会跑到  62fps
    + 17ms--58.8fps
    + 16ms--62.5fps
    + 75fps--13.33ms
    + 90fps--11.11ms
    + 120fps--8.33ms
5. 修改完~~需要重启~~关了远程重新连接基本就生效了，实在不行再重启试试
6. 测试网站：https://testufo.com/
7. 但是实际上在win11上能看rdp帧率，上面无论怎么设置，最终传输帧率还是30~40帧 = =