1、建立软链接

```shell
ln -s /Programs/nodejs/bin/npm /usr/local/bin
```

/Programs/nodejs/bin/npm是要被建立软链接的文件（夹），/usr/local/bin是目标路径

2、修改文件（夹）权限

```shell
 chmod -R a+rw hexo-cli
```

-R表示设置hexo-cli文件夹下的所有文件（夹）的权限

a表示所有用户

+表示 添加权限

r代表读的权限

w代表写的权限

hexo-cli是设置权限的文件夹

3、添加私有库

```shell
sudo apt-get install software-properties-common
```

4、git

创建新的git仓库

```shell
echo "# BlogOnGithubHexoCode" >> README.md
git init
git add README.md
git commit -m "first commit"
git remote add origin https://github.com/520BETTERME/BlogOnGithubHexoCode.git
git push -u origin master
```

提交

```shell
git remote add origin https://github.com/520BETTERME/BlogOnGithubHexoCode.git
git push -u origin master
```

5、安装常用软件

chrome

```shell
sudo apt-get install google-chrome-stable
```

