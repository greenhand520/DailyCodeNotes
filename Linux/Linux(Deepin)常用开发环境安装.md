今天手误把电脑上的deepin弄坏掉了，花了点时间重新配置了一些开发环境，把一些要点记录下来。

## 1、安装jdk

打开`/etc/profile`，在末尾添加

`/Programs/jdk1.8.0_181`是在官网下的jdk存放路径

```
export JAVA_HOME=/Programs/jdk1.8.0_181
export JRE_HOME=${JAVA_HOME}/jre  
export CLASSPATH=.:${JAVA_HOME}/lib:${JRE_HOME}/lib  
export PATH=${JAVA_HOME}/bin:$PATH
```

然后用root执行

```shell
source /etc/profile
```

## 2、安装tomcat

打开`${tomcat install path}/bin/startup.sh`，在最后一行之前添加：

```
#set java environment
export JAVA_HOME=/Programs/jdk1.8.0_181
export JRE_HOME=${JAVA_HOME}/jre
export CLASSPATH=.:%{JAVA_HOME}/lib:%{JRE_HOME}/lib
export PATH=${JAVA_HOME}/bin:$PATH
#tomcat
export TOMCAT_HOME=/Programs/apache-tomcat-9.0.10
```

之后运行startup.sh后，在浏览器输入localhost:8080测试

## 3、设置python默认为python3

在终端中以root身份登陆，输入下面两条命令即可

```shell
sudo update-alternatives --install /usr/bin/python python /usr/bin/python2 100
sudo update-alternatives --install /usr/bin/python python /usr/bin/python3 200
```

## 4、安装pip3

```shell
sudo apt-get install python3-pip
```

**更换pip源**

```shell
cd ~
mkdir .pip
cd .pip
touch pip.conf
```

pip.conf的内容为

```conf
[global]
index-url = http://pypi.douban.com/simple
[install]
trusted-host = pypi.douban.com
```

pip国内的一些镜像

  阿里云 <http://mirrors.aliyun.com/pypi/simple/> 
  中国科技大学 <https://pypi.mirrors.ustc.edu.cn/simple/> 
  豆瓣(douban) <http://pypi.douban.com/simple/> 
  清华大学 <https://pypi.tuna.tsinghua.edu.cn/simple/> 
  中国科学技术大学 <http://pypi.mirrors.ustc.edu.cn/simple/>

## 5、安装cmake

下载的cmake文件的路径为：`/Programs/cmake`，建立软链接

```shell
sudo ln -sf /Programs/cmake/bin/*  /usr/bin/
```
## 6、安装Qt4

```shell
sudo apt-get update
sudo apt-get install qt4*
```

## 7、安装nodejs

法一：apt-get安装

```shell
sudo apt-get install nodejs
sudo apt-get install npm
```

法二：手动下载包安装

`/Programs/nodejs`是nodejs的路径，在``/etc/profile``末尾添加

```
export NODE_HOME=/Programs/nodejs/bin
export PATH=$NODE_HOME:$PATH
```

npm镜像设置成淘宝镜像，加快下载速度

```shell
npm config set registry http://registry.npm.taobao.org/
```

升级npm

```
sudo npm install npm -g
```

升级node.js

```shell
npm install –g n
```

$ n latest(升级node.js到最新版)  or $ n stable（升级node.js到最新稳定版）

## 8、安装cuteCom串口调试助手

```shell
sudo apt-get install cutecom
```

## 9、安装mysql

```shell
sudo apt-get install mysql-server
sudo apt-get install mysql-client
sudo apt-get install libmysqlclient-dev
```

通过

```shell
sudo netstat -tap | grep mysql
```

检查是否安装成功，如果看到有mysql 的socket处于 listen 状态则表示安装成功，像下面这样

> tcp        0      0 localhost:mysql         0.0.0.0:*               LISTEN      13739/mysqld  

## 10、安装AndroidStudio

**添加环境变量**

在``/etc/profile``末尾添加

```
export ANDROID_HOME=/Programs/Android/android-sdk-linux
export PATH="$PATH:$ANDROID_HOME/tools:$ANDROID_HOME/platform-tools"
export ANDROID_SDK_HOME=/Programs/Android
export PATH=$ANDROID_SDK_HOME:$PATH
```

ANDROID_HOME是sdk的路径

ANDROID_SDK_HOME是.android的路径，avd存在这个路径下

**安装kvm**

```shell
sudo apt install qemu-kvm
```

**KVM问题**

KVM is required to run this AVD. /dev/kvm device: permission denied。

```shell
sudo chown -R ${username}:${pwd} /dev/kvm
```

## 11、安装jupyter notebook

```shell
pip3 install jupyter
```

