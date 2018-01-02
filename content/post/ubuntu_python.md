---
title: "ubuntu下python安装的正确打开方式"
date: 2017-12-31T10:31:01+08:00
---

我们在新买了一台服务器之后，由于ubuntu预装的python版本是2.7，所以我们经常需要安装python3系列。而ubuntu下python的安装并不方便直接apt install，因此我们需要自己手动安装并且有以下前置条件。

首先我们应该安装所有的前置依赖：
```sh
sudo apt-get update
sudo apt-get install python-dev
sudo apt-get install libffi-dev
sudo apt-get install libssl-dev
sudo apt-get install libxml2-dev
sudo apt-get install libxslt-dev
sudo apt-get install libmysqlclient-dev
sudo apt-get install libsqlite3-dev
sudo apt-get install zlib1g-dev
```
这些都是HTTP，SSL及数据库等相关依赖。之后我们去官网下载安装包，以3.6.4为例。

```sh
wget https://www.python.org/ftp/python/3.6.4/Python-3.6.4.tar.xz
```
下载完之后解压,注意啊虽然后缀是tar.xz但是按tar.xz的方法解压反而不行。
```sh
tar -xvf Python-3.6.4.tar.xz
```
之后便是一整套正常的流程了
```sh
cd Python-3.6.4
./configure
make
make install
```
这样就可以完整的安装python了，同时建议大家用virtualenv来隔绝自己的运行环境。值得一提的是如果你在安装之前没有添加这些依赖的话，重新添加了依赖也需要重新编译安装python。

