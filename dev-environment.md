## 本地开发环境：

- 解释器 eg. CPython
- 版本管理工具 virtualenv
- 包管理工具 pip3
- 编辑器使用 pycharm
- 数据库使用 mysql
- 系统使用 macos
- python两个版本，系统自带python2.7，独立安装3.6

## 遇到的问题

```
pip install:  /usr/local/opt/python/bin/python2.7: bad interpreter: No such file or directory

解决办法：
pip3 search 模块名
python3 -m pip install 模块名
```

```
# 安装requirements.txt依赖: 
pip install -r requirements.txt

# 生成requirements.txt文件: 
pip freeze > requirements.txt

```

```
方法1
virtualenv venv # 创建python 虚拟环境
source venv/bin/activate # 激活虚拟环境
 
方法2
mkdir myproject
cd myproject
python3 -m venv vent # 激活虚拟环境
```

```
# 安装mysql 链接器Connector/Python： 
pip install mysql-connector-python
```

```
help(object) 解释器交互交互状态下查看对象的信息
dir(object) 显示对象的信息
进入python解释器交互状态，print(os.__file__)即可
```

```
# Linux系统中安装依赖
# Debian, Ubuntu
sudo apt-get install <package_name>

# CentOS, Fedora
sudo yum install <package_name>
```

```
# 添加环境变量
export PATH=/opt/STM/STLinux-2.3/devkit/sh4/bin:$PATH

# 查看PATH环境变量
echo $PATH
```

```
# 用于重新执行刚修改的初始化文件，使之立即生效，而不必注销并重新登录
source <file_name>
```



