---
title: mac搭建python开发环境
img: /images/pythonenv.jpg
categories:
- Python
tags:
- Python
---
### 安装mysql
```Shell
# 安装
1. brew install mysql

# 启动
2. brew services start mysql
```
### 安装Python
```Shell
# 需要多版本可以使用pyenv
brew install python3.7

```
### 安装virtualenvwrapper
```Shell
# 新建虚拟环境的目录
mkdir .virtualenvs
    
# 安装virtualenvwrapper
pip install virtualenvwrapper

# 查找virtualenvwrapper.sh
sudo find / -name virtualenvwrapper.sh

# 执行virtualenvwrapper.sh
source /查找到的目录/virtualenvwrapper.sh

# 配置virtualenvwrapper
vim ~/.bash_profile

# 追加内容
VIRTUALENVWRAPPER_PYTHON=/usr/local/bin/python3.6
export WORKON_HOME=$HOME/.virtualenvs

# 配置生效
source ~/.bash_profile

# 创建虚拟环境
mkvirtualenv django

# 切换到创建的虚拟环境
workon django
```


