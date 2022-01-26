# 部署web应用程序到centos系统遇到的防火墙问题及解决


## 最初的目的

最近用go写了一个在线剪贴板的web小项目，在本地主机上调试了感觉还行，遂打算部署到甲骨文的VPS上（centos-8）。

环境搭建好了之后，成功运行在8080端口，`curl localhost:8080`也可以正常获取到网页，但是我的浏览器远程就是无法访问。在stackoverflow搜索了解，感觉是VPS防火墙的问题。并有了以下的解决步骤。此外，在解决问题的过程中还发现了一个有趣的小工具--cockpid,一个在web端管理VPS的工具，是centos和redhat系统自带的。

## 解决过程

在开始之前，首先去VPS提供商管理页面设置好相应的防火墙规则。之后在对VPS的防火墙设置规则。

1. 查看VPS防火墙状态：
    `sudo systemctl status firewalld`
    ![20220126-2207-OPDArI-c400](https://cdn.jsdelivr.net/gh/ZevaXu/picupload@master/uPic/20220126-2207-OPDArI.png)
    可以看到防火墙是正常运行的。
2. 查看VPS防火墙开启的端口和服务
    `firewall-cmd --list-all`
    ![20220126-2209-t3zBvo-c300](https://cdn.jsdelivr.net/gh/ZevaXu/picupload@master/uPic/20220126-2209-t3zBvo.png)
    可以看到，我的VPS开启了cockpit dhcpv6-client http ssh这些个服务，以及一个8080端口（这是我主动开启之后的显示，一开始只有cockpit dhcpv6-client ssh这三项）。
    
    如果想要查看某个服务占据的端口，可以用`grep ssh /etc/services`这个命令(以ssh为例)，可以看到ssh使用的是本机22端口。
    ![20220126-2213-ILsh7J-c400](https://cdn.jsdelivr.net/gh/ZevaXu/picupload@master/uPic/20220126-2213-ILsh7J.png)
    
3. 开启希望的服务和端口。
    开启http服务（即80端口）：`sudo firewall-cmd --add-service=http --permanent`
    开启自定义端口：`firewall-cmd --zone=public --permanent --remove-port 8080`
    不仅可以开端口，也可以关：`firewall-cmd --zone=public --permanent --remove-service http`
    
    但是最后一定要重载以下使之生效：`firewall-cmd --reload`
    
    之后可以再用`firewall-cmd --list-all`命令查看有没有成功。
    
4.经过上述操作，我已经可以通过浏览器正常访问到我部署的web服务了，大功告成！
![20220126-2256-WOB2A3-c480](https://cdn.jsdelivr.net/gh/ZevaXu/picupload@master/uPic/20220126-2256-WOB2A3.png)

## Cockpid

在之前**查看VPS防火墙开启的端口和服务**一步骤中，我看到有个名叫Cockpid的服务，一开始以为是个恶意程序（挖坑、肉鸡）,谨慎的上谷歌查了查，原来这家伙是centos自带的一个web端LINUX系统管理程序，这是它的官网（https://cockpit-project.org），[这是](https://www.hangge.com/blog/cache/detail_3024.html)它的一些介绍,以及[安装过程](https://www.linuxtechi.com/install-use-cockpit-tool-centos8-rhel8/)。

安装好之后，只需要打开URL（VPD的域名或IP地址:9090），即可使用。下图是cockpid的登陆界面：
![20220126-2232-2ksbGO-c480](https://cdn.jsdelivr.net/gh/ZevaXu/picupload@master/uPic/20220126-2232-2ksbGO.png)

我的VPS设置为ssh keys方式登陆，cockpid网页似乎只能通过用户名和密码登录，于是我先建了一个用户并登录，[这是](https://www.cyberciti.biz/faq/create-a-new-user-account-in-centos-7-8-linux/)有关linux如何创建用户的说明。

以下是cockpid提供的一些功能截图：
![20220126-2249-osQB5o-c480](https://cdn.jsdelivr.net/gh/ZevaXu/picupload@master/uPic/20220126-2249-osQB5o.png)





参考：
[1] [How to open and close ports on RHEL 8 / CentOS 8 Linux](https://linuxconfig.org/redhat-8-open-and-close-ports)
[2] [How To Open Port 80 on CentOS](https://linuxhint.com/open-port-80-centos/)
[3] [How to List Open Ports in Firewalld](https://linuxhint.com/list_open_ports_firewalld/)

