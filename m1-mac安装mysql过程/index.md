# M1 Mac安装mysql过程


## 设备信息
![20220121-2211-vHHbMw-c320](https://cdn.jsdelivr.net/gh/ZevaXu/picupload@master/uPic/20220121-2211-vHHbMw.png)

## 安装过程
通过homebrew安装，默认安装路径：`/opt/homebrew/var`，以下是[homebrew官网](https://formulae.brew.sh/formula/)搜索mysql的信息：
![20220121-2214-0D5rmw-c320](https://cdn.jsdelivr.net/gh/ZevaXu/picupload@master/uPic/20220121-2214-0D5rmw.png)
可以看到是原生支持m1芯片的。
在终端输入下列命令，进行安装：
```
brew install mysql
```
等待安装完毕后，可以使用下列命令开启或停止mysql服务：
```
sudo mysql.server start  //开启服务
sudo mysql.server stop   //关闭服务
sudo mysql.server restart  //重启服务
sudo mysql.server status  //查看状态
```
注意需要输入Mac开机密码来确认上述命令的执行。

初次开启服务，mysql是没有设置密码的，即密码是空白，可使用以下命令来进行一些账户安全设置：
```
mysql_secure_installation
```
一切设置完毕就可以正常使用了
![20220121-2225-IkYoR9-c320](https://cdn.jsdelivr.net/gh/ZevaXu/picupload@master/uPic/20220121-2225-IkYoR9.png)

## 卸载mysql

首先用以下命令卸载
```
brew uninstall mysql
```
要想彻底清除数据库文件，还要去`/opt/homebrew/var`目录执行：
`sudo rm -rf mysql`命令。


