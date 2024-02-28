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



## 示例

1、查看已安装的包

conda list
2、更新所有包

conda upgrade --all
3、安装包

conda install package_name
4、删除包

conda remove package_name
5、更新包

conda update package_name
6、不知道包名要找包

conda search name
7、用conda建立虚拟环境

conda create -n env_name list_of_packages
其中 -n 代表 name，env_name 是需要创建的环境名称，list of packages 则是列出在新环境中需要安装的工具包。

例如，当我安装了 Python3 版本的 Anaconda 后，默认的 root 环境自然是 Python3，但是我还需要创建一个 Python 2 的环境来运行旧版本的 Python 代码，最好还安装了 pandas 包，于是我们运行以下命令来创建：

conda create -n py2 python=2.7 pandas
细心的你一定会发现，py2 环境中不仅安装了 pandas，还安装了 numpy 等一系列 packages，这就是使用 conda 的方便之处，它会自动为你安装相应的依赖包，而不需要你一个个手动安装。

8、进入虚拟环境

conda activate env_name
9、退出虚拟环境

conda deactivate
10、删除名为 env_name 的环境

conda env remove -n env_name
11、显示所有的环境：

conda env list
12、当分享代码的时候，同时也需要将运行环境分享给大家，执行如下命令可以将当前环境下的 package 信息存入名为 environment 的 YAML 文件中

conda env export > environment.yaml
13、使用别人生成的yaml文件创建环境


conda env create -f environment.yaml
