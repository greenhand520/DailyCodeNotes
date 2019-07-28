# 开发环境

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

```shell
#set java environment
export JAVA_HOME=/programs/jdk1.8.0_212
export JRE_HOME=${JAVA_HOME}/jre
export CLASSPATH=.:%{JAVA_HOME}/lib:%{JRE_HOME}/lib
export PATH=${JAVA_HOME}/bin:$PATH
#tomcat
export TOMCAT_HOME=/programs/apache-tomcat-9.0.8
```

之后运行startup.sh后，在浏览器输入localhost:8080测试

## 3、安装mysql

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

## 4、Python

### 1）设置python默认为python3

在终端中以root身份登陆，输入下面两条命令即可

```shell
sudo update-alternatives --install /usr/bin/python python /usr/bin/python2 100
sudo update-alternatives --install /usr/bin/python python /usr/bin/python3 200
```

### 2）安装pip pip3

```shell
sudo apt-get install python-pip
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
index-url = http://mirrors.aliyun.com/pypi/simple/
[install]
trusted-host = mirrors.aliyun.com
```

pip国内的一些镜像

  阿里云 <http://mirrors.aliyun.com/pypi/simple/> 
  中国科技大学 <https://pypi.mirrors.ustc.edu.cn/simple/> 
  豆瓣(douban) <http://pypi.douban.com/simple/> 
  清华大学 <https://pypi.tuna.tsinghua.edu.cn/simple/> 
  中国科学技术大学 <http://pypi.mirrors.ustc.edu.cn/simple/>

### 3）安装虚拟环境

```shell
pip3 install python-virtualenv
pip3 install virtualenvwrapper
```

创建用于存储虚拟环境的文件夹

```shell
mkdir /programs/Appdata/.virtualenvs
```

查找virtualenvwrapper.sh的路径

```shell
whereis virtualenvwrapper.sh
```

我用的zsh, 故打开 ~/.zshrc在最后添加下面这些

```shell
export WORKON_HOME=/programs/Appdata/.virtualenvs
source ~/.local/bin/virtualenvwrapper.sh
```

然后

```shell
source  ~/.zshrc
```

创建新的虚拟环境: mkvirtualenv -p python路径 [虚拟环境名称]

默认是python2.5

```shell
mkvirtualenv -p python3 python-web
```

删除虚拟环境: rmvirtualenv [虚拟环境名称]

进入: workon [虚拟环境名称]

退出: deactivate

## 5、安装cmake

下载的cmake文件的路径为：`/Programs/cmake`，建立软链接

```shell
sudo ln -sf /Programs/cmake/bin/*  /usr/bin/
```

## 6、安装nodejs

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

## 7、docker

https://www.docker.com/

**卸载旧版本**

```shell
sudo apt-get remove docker docker-engine docker.io containerd runc
```

**安装**

1. Update the `apt` package index:

    ```sh
    sudo apt-get update
    ```

2. Install packages to allow `apt` to use a repository over HTTPS:

    ```shell
    sudo apt-get install \
        apt-transport-https \
        ca-certificates \
        curl \
        gnupg-agent \
        software-properties-common
    ```

3. Add Docker’s official GPG key:

    ```
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
    ```

    Verify that you now have the key with the fingerprint `9DC8 5822 9FC7 DD38 854A E2D8 8D81 803C 0EBF CD88`, by searching for the last 8 characters of the fingerprint.

    ```shell
    sudo apt-key fingerprint 0EBFCD88
        
    pub   rsa4096 2017-02-22 [SCEA]
          9DC8 5822 9FC7 DD38 854A  E2D8 8D81 803C 0EBF CD88
    uid           [ unknown] Docker Release (CE deb) <docker@docker.com>
    sub   rsa4096 2017-02-22 [S]
    ```

4. Use the following command to set up the **stable** repository. To add the **nightly** or **test** repository, add the word `nightly` or `test` (or both) after the word `stable` in the commands below. [Learn about **nightly** and **test** channels](https://docs.docker.com/install/).

    ```shell
    sudo add-apt-repository \
       "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
       $(lsb_release -cs) \
       stable"
    ```

# 开发软件

## 1、安装AndroidStudio

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
sudo apt install qemu qemu-kvm libvirt-bin  bridge-utils  virt-manager
```

**KVM问题**

KVM is required to run this AVD. /dev/kvm device: permission denied。

```shell
sudo chown -R ${username}:${pwd} /dev/kvm
```

## 2、安装jupyter notebook

```shell
pip3 install jupyter
```

## 3、VScode

https://code.visualstudio.com/

# 常用软件

## 1、typora

```shell
# or run:
# sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys BA300B7755AFCFAE
wget -qO - https://typora.io/linux/public-key.asc | sudo apt-key add -
# add Typora's repository
sudo add-apt-repository 'deb https://typora.io/linux ./'
sudo apt-get update
# install typora
sudo apt-get install typora
```

## 2、cuteCom串口调试助手

```shell
sudo apt-get install cutecom
```

## 3、shadowsocks-qt5

https://github.com/shadowsocks/shadowsocks-qt5

## 4、zsh

```shell
# 首先安装zsh
sudo apt-get install zsh
# 替换系统的bash为zsh
chsh -s /bin/zsh
# 安装oh-my-zsh
sudo apt-get install git #(如果没有安装git,先执行这一步)
sudo wget https://github.com/robbyrussell/oh-my-zsh/raw/master/tools/install.sh -O - | sh
# 安装autojump, zsh-syntax-highlight, zsh-autosuggestion
sudo apt install autojump
sudo apt-get install zsh-syntax-highlighting
git clone https://github.com/zsh-users/zsh-autosuggestions ~/.zsh/zsh-autosuggestions
gedit ~/.zshrc
# 文末添加这几句 
# source /usr/share/autojump/autojump.zsh
# source /usr/share/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh
# source ~/.zsh/zsh-autosuggestions/zsh-autosuggestions.zsh
source ~/.zshrc
# 重启终端
```

## 5、搜狗输入法

```shell
sudo apt install fcitx-bin
# 然后下载搜狗deb包：搜狗输入法 for linux https://pinyin.sogou.com/linux/?r=pinyin
sudo dpkg -i sogoupinyin_2.2.0.0108_amd64.deb
sudo apt install -f # 如果出现依赖错误输入这行处理，执行完后，继续输入上面的命令
```

## 6、WPS

https://www.wps.cn/product/wpslinux/

```shell
sudo dpkg -i wps-office_10.1.0.6757_amd64.deb
sudo apt install -f # 如果出现依赖错误输入这行处理
```

装完后打开wps会发现提示缺少字体，这时候我们就需要去安装一下ubuntu系统没有的中文字体，理论上，你只需要下面这些字体就可以解决问题：

WPS Office 所需字体：wingding.ttf、webdings.ttf、symbol.ttf、WINGDNG3.TTF、WINGDNG2.TTF、MTExtra.ttf

但是，既然要使用wps，其他中文字体也是要用到的，还不如去Windows系统下C:\Windows\Fonts把所有用的到的字体全靠过来，也就百来兆大小

然后，在ubuntu中创建 Windows 字体存放路径

```shell
sudo mkdir /usr/share/fonts/win-fonts
```

拷贝字体到wiondow-fonts录下

```shell
sudo cp /home/fonts/*  /usr/share/fonts/win-fonts
```

更新字体缓存

```shell
sudo mkfontscale
sudo mkfontdir
sudo fc-cache
```

## 7、网易云音乐

https://music.163.com/#/download

## 8、MPV播放器

```sgell
sudo apt-get install mpv
```

**一些快捷键**https://mpv.io/manual/stable/

左和右

向后/向前寻找5秒。Shift +箭头执行1秒精确搜索。

上和下

向前/向后寻找1分钟。Shift +箭头执行5秒精确搜索）。

[和]

将当前播放速度降低/增加10％。

{和}

当前播放速度减半/双倍。

BACKSPACE

将播放速度重置为正常。

<和>

在播放列表中前进/后退。

p / SPACE

暂停（再次按下取消暂停）。

/和*

减少/增加音量。

9和0

减少/增加音量

## 9、deepin-wine

github链接: https://github.com/wszqkzqk/deepin-wine-ubuntu

```shell
git clone https://github.com/wszqkzqk/deepin-wine-ubuntu
cd deepin-wine-for-ubuntu
sudo chmod a+x install.sh
sudo ./install
```

TIM: https://mirrors.aliyun.com/deepin/pool/non-free/d/deepin.com.qq.office/

QQ: https://mirrors.aliyun.com/deepin/pool/non-free/d/deepin.com.qq.im/

WeChat: https://mirrors.aliyun.com/deepin/pool/non-free/d/deepin.com.wechat/

**关于托盘**

Ubuntu 18.04 下（Gnome 桌面）：

安装 Gnome Shell 插件：[TopIcons Plus](https://extensions.gnome.org/extension/1031/topicons/)

手动更改配置（winecfg）

执行 `WINEPREFIX=~/.deepinwine/容器名称 deepin-wine winecfg` 即可，也可以用此方法来调整缩放问题

## 10、deepin desktop environment

```shell
sudo add-apt-repository ppa:leaeasy/dde
sudo apt install dde dde-file-manager
# 中途会遇到让你选择一个显示管理器,默认的gdm3即可
sudo apt install deepin-gtk-theme
```

## 11、wine

```shell
sudo apt-get install wine-stable
sudo apt install winetricks   
winetricks msxml3
winetricks donet20
winetricks donet40
```



## 12、vmware

下载安装包 https://www.vmware.com/cn/products/workstation-pro/workstation-pro-evaluation.html

下载后以管理员权限运行安装包

卸载

```shell
vmware-installer -u vmware-workstation
```

## 13、aria2

```shell
sudo apt-get install aria2
```

**使用:**

```shell
# 下载后面链接的内容,直接保存在当前文件夹
aria2c http://xxxx
```

## 14、gitg

git跟踪，并不能pull push

```shell
sudo apt-get install gitg
```

## 15、fish

与bash不太一样，部分shell可能不兼容fish

```shell
sudo apt-get install fish
# 安装oh-my-fish
curl -L https://get.oh-my.fish | fish

curl -L https://get.oh-my.fish > install
fish install --path=~/.local/share/omf --config=~/.config/omf
```

这里不用fish作为默认解释器了

## 16、CHM阅读器

```shell
sudo apt-get install kchmviewer
```

# 其他优化

## 1、归档管理器支持rar和7z格式

```shell
# rar 7z支持
sudo apt install p7zip-full unrar
```

## 2、更改密码

问题：new and old password are too similar

以管理员权限更改密码

```shell
sudo passwd $(username)
```

