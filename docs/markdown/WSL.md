# Windows Subsystem for Linux

## 问题

### 解决WSL2占用内存过多问题（Docker on WSL2: VmmemWSL）

Ref. https://learn.microsoft.com/en-us/windows/wsl/wsl-config

Ref. https://blog.csdn.net/qq_36743482/article/details/132879300

#### 临时

直接杀后台 `wsl --shutdown`

#### 配置文件


创建文件 C:\Users\UserName\\.wslconfig

Ref. https://learn.microsoft.com/en-us/windows/wsl/wsl-config#example-wslconfig-file

关键参数：下面两个就够了

[wsl2]  
memory=3GB  
swap=3GB

然后重启wsl即可