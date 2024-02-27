# [Conda常用命令合集](https://zhuanlan.zhihu.com/p/363904808)

from https://zhuanlan.zhihu.com/p/363904808

## 1. 获取版本号/帮助

|   |   |
|  :----  | :-----  |
|获取版本号|conda -V|
| |conda --version|
|获取帮助|conda -h|
| |conda --help|
|获取环境相关命令的帮助	|conda env -h|
|||


所有 --单词 都可以用 -单词首字母来代替	

比如 -version 可以用 -V来代替

只不过有的是大写，有的可能是小写


## 2. 环境相关


|   |   |
|  :----  | :-----  |
|创建环境	|conda create -n environment_name|
|创建指定python版本下包含某些包的环境	|conda create -n environment_name python=3.7 numpy scipy|
|进入环境	|conda activate environment_name|
|退出环境	|conda deactivate|
|删除环境	|conda remove -n yourname --all|
|列出环境	|conda env list / conda info -e|
|复制环境	|conda create --name new_env_name --clone old_env_name|
|指定目录下生成环境yml文件	|conda env export > 目录/environment.yml|
|从yml文件创建环境	|conda env create -n env_name -f environment.yml|
|||




## 4. 管理包
对包的管理是在某个环境下进行的，先进入特定环境再进行包的操作比较好，不会出现把本该安装在A环境中的包安装在了B环境中这种情况。

|||
|  :----  | :-----  |
|安装包	|conda instal package_name|
|查看当前环境包列表	|conda list|
|查看指定环境包列表	|conda list -n environment_name|
|查看conda源中包的信息	|conda search package_name|
|更新包	|conda update package_name|
|删除包	|conda remove package_name|
|清理无用的安装包	|conda clean -p|
|清理tar包	|conda clean -t|
|清理所有安装包及cache	|conda clean -y --all|
|更新anaconda	|conda update annaconda|
|||

最后三个清理命令类似于清理手机上的安装包、缓存，不会删除某个库，只是删除已经安装完成的那些安装包。

## 5. 更换conda源


### windows

conda命令行：

`conda config --add channels https://mirrors.hit.edu.cn/anaconda/pkgs/main/`

`conda config --add channels https://mirrors.hit.edu.cn/anaconda/pkgs/free/`

设置搜索时显示通道地址

`conda config --set show_channel_urls yes`

### linux:

将以上配置文件写在~/.condarc中 vim ~/.condarc
 ​
### 显示现有安装源

`conda config --show channels`

### 恢复默认源

`conda config --remove-key channels`

### 移除某个源

`conda config --remove channels https://mirrors.cloud.tencent.com/anaconda/pkgs/pro/`
