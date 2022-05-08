# 甲骨文VPS搭建trojan步骤


# 甲骨文VPS搭建trojan步骤

### VPS准备

需要一台IP没有被墙的VPS，也就是不开代理的情况下可以通过SSH连接到 VPS。我自己用的是甲骨文的机器，甲骨文机器可以在不清除数据的情况下随时更改IP地址，这样的好处是，如果后续该ip被墙了，换一个ip，改一下域名解析就可以接着用。

<img src="https://cdn.jsdelivr.net/gh/ZevaXu/picupload@master/uPic/20220320-1725-FOHJQg.png" alt="20220320-1725-FOHJQg" style="zoom: 33%;" />

<img src="https://cdn.jsdelivr.net/gh/ZevaXu/picupload@master/uPic/20220320-1727-KYznYj.png" alt="20220320-1727-KYznYj" style="zoom:33%;" />

### 防火墙设置

ssh连接到VPS后，现在VPS提供商（我的是甲骨文）控制面板里更改防火墙设置。放行需要用到的端口（这里不建议打开所有端口）。

<img src="https://cdn.jsdelivr.net/gh/ZevaXu/picupload@master/uPic/20220320-1727-CyQeWT.png" alt="20220320-1727-CyQeWT" style="zoom:33%;" />

之后，再去VPS实例里面更改防火墙设置，我的机器是centos8系统，用Firewalld命令更改防火墙设置。

```
# 列出已开启的端口/服务
firewall-cmd --list-all 

# 开启端口
firewall-cmd --zone=public --permanent --add-port 8080/tcp

# 重载配置
firewall-cmd --reload
```

<img src="https://cdn.jsdelivr.net/gh/ZevaXu/picupload@master/uPic/20220320-1734-3CUKHN.png" alt="20220320-1734-3CUKHN" style="zoom: 50%;" />

### 安装docker以及拉取要用的镜像

安装docker参照[docker官方文档](https://docs.docker.com/get-started/)

安装好docker后拉取[trojan镜像](https://github.com/Jrohy/trojan)。

```
# 拉取数据库，此处的端口映射、root密码、数据库名称都可以改
docker run --name trojan-mariadb --restart=always -p 3308:3306 -v /root/trojan_docker/mariadb:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=trojan_zeva -e MYSQL_ROOT_HOST=% -e MYSQL_DATABASE=trojan -d mariadb:10.2
```

此处需要注意，容器端口的选择不能是已经被占用的端口，不然会报错。

<img src="https://cdn.jsdelivr.net/gh/ZevaXu/picupload@master/uPic/20220320-1744-5p0Wcx.png" alt="20220320-1744-5p0Wcx" style="zoom:50%;" />

```
#拉取trojan镜像
docker run -it -d --name trojan --net=host --restart=always -v /root/trojan_docker/root/:/root/ --privileged jrohy/trojan init

# 进入容器
docker exec -it trojan bash

#开启trojan 会自动开始安装 按提示输入信息即可
trojan
```

此时可能有一些问题：

1. 解析问题

    <img src="/Users/zhxu/Library/Application%20Support/typora-user-images/image-20220320185951719.png" alt="image-20220320185951719" style="zoom:33%;" />

​	选一个证书发行机构（推荐zeroSSL），输入域名（已经解析到VPS的ip）,如果开启了cloudflare的代理功能，此处可能报错，域名解析地址不正确。再输入邮箱，等待脚本自动申请证书。

如果有输出类似如下，说明证书申请成功：

<img src="https://cdn.jsdelivr.net/gh/ZevaXu/picupload@master/uPic/20220320-1911-X3XUBT.png" alt="20220320-1911-X3XUBT" style="zoom:50%;" />

没有上图输出，那就是申请失败，无法开启trojan。此时需要做的是：

1. 查看VPS上是否开启了其他web服务，如nginx、web应用等。可能是它们占用了申请证书时需要用到的端口，暂时关闭这些服务，再尝试申请。
2. 可能你用的域名已经在证书发行机构申请过了域名。此时去域名解析处添加一个记录，用新添加的域名尝试。
3. 可能是80、443端口没有开放。此时开饭这些端口再尝试。

成功申请好证书之后，只需要在管理界面重新启动一次trojan即可。

### BBR加速和伪装网站

#### 开启bbr加速

> BBR是谷歌开发出了一种加速网络传输协议 TCP 的新算法，这种算法通过优化传输速度，避免路由堵塞现象的产生。[BBR](https://www.leixue.com/so/BBR) 利用瓶颈带宽和往返传播时间，被认为是迄今为止跨越不同路由发送数据的最快方法，当数据路由拥挤时，能够更有效地处理流量。目前 Google 已经将 BBR 投入 YouTube 使用。有消息透露，BBR 通过优化使 YouTube 流量平均增长了 4%，在其他一些方面则达到了 14%。

centos8 只能开启bbr，一些其他的bbr魔改会卸载一些系统内核，导致VPS无法连接。

**1.开启BBR**

```
echo "net.core.default_qdisc=fq" >> /etc/sysctl.conf
echo "net.ipv4.tcp_congestion_control=bbr" >> /etc/sysctl.conf
```

**保存退出后，执行下边的命令使配置生效**

```
sysctl -p
```

**2.检查 BBR 是否开启成功**

```
sysctl -n net.ipv4.tcp_congestion_control
lsmod | grep bbr
```

<img src="https://cdn.jsdelivr.net/gh/ZevaXu/picupload@master/uPic/20220320-1931-KOZu4D.png" alt="20220320-1931-KOZu4D" style="zoom:50%;" />

#### 伪装站点

安装好此trojan后，自带一个web管理界面，需要的可以开启，我个人觉得不需要，防止被探查到，就关闭了。下面是相关命令。

```
# 开启
systemctl start trojan-web

#关闭
systemctl stop trojan-web

# 允许自启动
systemctl enable trojan-web
```

利用nginxproxymanager部署站点

参照[链接](https://github.com/NginxProxyManager/nginx-proxy-manager/issues/280)

```
cd {path-to-install-npm}

docker-compose down

vim docker-compose.yml

version: '3'
services:
  app:
    image: 'jc21/nginx-proxy-manager:latest'
    restart: unless-stopped
    ports:
      - '80:80'
      - '81:81'
      - '443:443'
    volumes:
      - ./data:/data
      - ./letsencrypt:/etc/letsencrypt
      - ./websites/:/mnt/user/appdata/NginxProxyManager/websites/
    
docker-compose up -d

```

进入npm管理后台，新增加一天反代

```
root /mnt/user/appdata/NginxProxyManager/websites/test;
index index.html;
location / {
    try_files $uri /index.html /index.html;
}
```

在path-to-install-npm/websites/ 目录下放入编辑好的html文件index.html（可以从github上clone一个静态博客等）。

<img src="https://cdn.jsdelivr.net/gh/ZevaXu/picupload@master/uPic/20220320-2017-J5D05B.png" alt="20220320-2017-J5D05B" style="zoom:50%;" />

然后再访问下代理的域名，看看是不是指向了html文件。
