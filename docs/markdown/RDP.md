https://zhuanlan.zhihu.com/p/600706130
https://zhuanlan.zhihu.com/p/445216327

适用于 Windows 家庭版 开启远程桌面 Remote Desktop Protocol
一、增加入站规则
1、在Win11任务栏搜索栏中输入“高级安全”，打开“高级安全 Windows Defender 防火墙”菜单

2、点击左侧“入站规则”，再点击右侧“新建规则”

3、新入站规则的具体设置

二、使用 RDPWrap 套件进行激活 RDP Wrapper Library v1.6.2
https://github.com/stascorp/rdpwrap/releases

1、、install.bat 使用管理员身份运行

出现如图的 “RDP Wrapper Library is already installed”字样即安装成功。

2、update.bat 使用管理员身份运行
这个用来更新的批处理文件是要向GitHub发送请求的，由于懂得都懂的原因，一些同学在网络不好的情况下容

3、RDPConf.exe 使用管理员身份运行

图中几处绿色的地方，如果都绿了，说明已经设置成功。

4、右键以管理员身份运行RDPCheck.exe，出现远程桌面登录界面则表示安装正常。

二、问题以及解决方法
1、运行RDPConf.exeWrapped state和Service state都正常，但是Listener State显示not Listening 【not supported】
C:\Program Files\RDP Wrapper\rdpwrap.ini 里面有个链接，手动打开链接，copy内容到该文件中，保存即可解决


RDP 微软账号登录问题 https://zhuanlan.zhihu.com/p/600623397

设置 - 账户 - 登陆选项 - 为了提高安全性，仅允许对此设备上的Microsoft账户使用Windows Hello登录  把这个关闭 
然后就能使用微软账号和密码登录了（微软账号的密码，不是pin）