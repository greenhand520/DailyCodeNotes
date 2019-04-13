### 1、解决安装透明任务栏问题

装了那个dock透明的deb包后，dock栏的体验变得很差，卸载后开机dock就加载不出来，需要将系统的dock卸载再重新安装。

```shell
sudo apt-get remove sm-dde-dock     ##卸载掉透明软件包
sudo apt-get remove dde-dock     ##卸载掉dock
sudo apt-get install dde-dock     ##重新安装dock
```

### 2、设置python默认为python3

在终端中以root身份登陆，输入下面两条命令即可

```shell
update-alternatives --install /usr/bin/python python /usr/bin/python2 100
update-alternatives --install /usr/bin/python python /usr/bin/python3 200
```