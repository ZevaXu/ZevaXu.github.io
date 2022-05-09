# Linux账号管理


## Linux 账号与群组

### UID与GID

每个登陆的使用者至少都会取得两个 ID ，一个是使用者 ID （User ID ，简称
UID）、一个是群组 ID （Group ID ，简称 GID）

* /etc/passwd :存放uid相关信息

    ```
    用户名：密码：UID（用户ID）：GID（组ID）：描述性信息：主目录：默认Shell
    ```

* /etc/group: 存放gid群组信息

* /etc/shadow：存放密码信息

    ```
    用户名：加密密码：最后一次修改时间：最小修改时间间隔：密码有效期：密码需要变更前的警告天数：密码过期后的宽限时间：账号失效时间：保留字段
    ```

    [详情](http://c.biancheng.net/view/840.html)

```
# 查看用户id
id ubuntu

# 查看支持的群组 第一个输出的群组即为有效群组
groups

# 切换有效群组
newgrp docker
```

![20220422-1133-rAyXuH](https://cdn.jsdelivr.net/gh/ZevaXu/picupload@master/uPic/20220422-1133-rAyXuH.png)

![20220422-1237-rXXBEp](https://cdn.jsdelivr.net/gh/ZevaXu/picupload@master/uPic/20220422-1237-rXXBEp.png)



## 账号管理

### useradd

```
useradd [-u UID] [-g 初始群组] [-G 次要群组] [-mM] [-c 说明] [-d 主文件夹路径] [-s shell] 账号名

-u 数字，指定一个uid
-g 初始群组
-G 可加入的其他群组
-M 强制，不创建主文件夹
-m 默认，创建主文件夹，权限700
-c 说明内容
-d 指定某个路径为主文件夹
-r 创建系统账号
-s 接shell
-e 账号失效日期'YYYY-MM-DD',对于shadow第8个字段
-f 指定密码失效时间 0表示立刻失效，-1 表示永不失效

# 创建账号
useradd test

# 设置密码
passwd test
```

* 在 /etc/passwd 里面创建一行与帐号相关的数据，包括创建 UID/GID/主文件夹等；
* 在 /etc/shadow 里面将此帐号的密码相关参数填入，但是尚未有密码；
* 在 /etc/group 里面加入一个与帐号名称一模一样的群组名称；
* 在 /home 下面创建一个与帐号同名的目录作为使用者主文件夹，且权限为 700
* /etc/default/useradd 文件包含创建用户的初始化详细设定

![20220422-1253-M8bviM](https://cdn.jsdelivr.net/gh/ZevaXu/picupload@master/uPic/20220422-1253-M8bviM.png)

### passwd

设置密码

```
passwd [-l u S] [--stdin] 账号名称

--stdin 可以通过前一个管线的数据，作为密码的输入
-l 加锁，使密码失效
-u 解锁
-S 列出密码相关参数，shadow中大部分信息
-n shadow(4),不可修改的天数
-x shadow(5),必须修改的天数
-w shadow(6),过期警告天数
-i 日期，shadow(7),失效日期

echo "abc543CC" | passwd --stdin vbird2

```

![20220422-1306-jd0lyr](https://cdn.jsdelivr.net/gh/ZevaXu/picupload@master/uPic/20220422-1306-jd0lyr.png)

### usermod

对账号信息进行修改

```
usermod [-cdegGlsuLU] username 选项与参数：

-c ：后面接帐号的说明，即 /etc/passwd 第五栏的说明栏，可以加入一些帐号的说明。
-d ：后面接帐号的主文件夹，即修改 /etc/passwd 的第六栏；
-e ：后面接日期，格式是 YYYY-MM-DD 也就是在 /etc/shadow 内的第八个字段数据啦！
-f ：后面接天数，为 shadow 的第七字段。
-g ：后面接初始群组，修改 /etc/passwd 的第四个字段，亦即是 GID 的字段！
-G ：后面接次要群组，修改这个使用者能够支持的群组，修改的是 /etc/group 啰～
-a ：与 -G 合用，可“增加次要群组的支持”而非“设置”喔！
-l ：后面接帐号名称。亦即是修改帐号名称， /etc/passwd 的第一栏！
-s ：后面接 Shell 的实际文件，例如 /bin/bash 或 /bin/csh 等等。
-u ：后面接 UID 数字啦！即 /etc/passwd 第三栏的数据；
-L ：暂时将使用者的密码冻结，让他无法登陆。其实仅改 /etc/shadow 的密码栏。
-U ：将 /etc/shadow 密码栏的 ! 拿掉，解冻啦！

# 增加描述
usermod -c "测试" test
```

![20220422-1312-fnHL1t](https://cdn.jsdelivr.net/gh/ZevaXu/picupload@master/uPic/20220422-1312-fnHL1t.png)

### userdel

删除账户信息以及主文件夹

通常我们要移除一个帐号的时候，你可以手动的将 /etc/passwd 与/etc/shadow 里头的该帐号取消即可！一般而言，如果该帐号只是“暂时不启用”的话，那么将 /etc/shadow 里头帐号失效日期 （第八字段） 设置为 0 就可以让该帐号无法使用，但是所有跟该帐号相关的数据都会留下来！ 使用 userdel 的时机通常是“你真的确定不要让该用户在主机上面使用任何数据了

```
userdel [-r] 用户名

-r 连同主文件夹

userdel -r test
```

![20220422-1321-068xq5](https://cdn.jsdelivr.net/gh/ZevaXu/picupload@master/uPic/20220422-1321-068xq5.png)

### chown

````
chown [-R] 账号名称 文件或目录

chown [-R] 账号名称:用户组名称 文件或目录

[-R] ：递归
````



## 群组管理

### groupadd 

增加群组

```
groupadd [-g gid] [-r] 群组名称

-g ：后面接某个特定的 GID ，用来直接给予某个 GID ～
-r ：创建系统群组啦！与 /etc/login.defs 内的 GID_MIN 有关

groupadd test

```

### groupmod

修改现有的群组

```
 groupmod [-g gid] [-n group_name] 群组名
选项与参数：
-g ：修改既有的 GID 数字；
-n ：修改既有的群组名称

groupmod -g 1005 -n ttest test
```

![20220422-1335-EmsuaY](https://cdn.jsdelivr.net/gh/ZevaXu/picupload@master/uPic/20220422-1335-EmsuaY.png)

### groupdel

删除现有的群组

## 账号切换

### su

身份切换

```
su [-lm] [-c 指令] [username]

- ：单纯使用 - 如“ su - ”代表使用 login-shell 的变量文件读取方式来登陆系统；
若使用者名称没有加上去，则代表切换为 root 的身份。
-l ：与 - 类似，但后面需要加欲切换的使用者帐号！也是 login-shell 的方式。
-m ：-m 与 -p 是一样的，表示“使用目前的环境设置，而不读取新使用者的配置文件”
-c ：仅进行一次指令，所以 -c 后面可以加上指令喔！

su - test
```



### sudo 

由于 sudo 可以让你以其他用户的身份执行指令，通常是root

因此并非所有人都能够执行 sudo ， 而是仅有规范到 /etc/sudoers内的用户才能够执行 sudo 这个指令.

```
sudo [-b] [-u 新使用者帐号]
选项与参数：
-b ：将后续的指令放到背景中让系统自行执行，而不与目前的 shell 产生影响
-u ：后面可以接欲切换的使用者，若无此项则代表切换身份为 root 。

sudo -u sshd touch /tmp/mysshd

```

## 主机使用者信息

### 查询使用者

#### w

查询目前已登录在系统的使用者

```
w
# 第一行显示目前的时间、开机 （up） 多久，几个使用者在系统上平均负载等；
# 第二行只是各个项目的说明，
# 第三行以后，每行代表一个使用者。如上所示，dmtsai 登陆并取得终端机名 tty2 之意。
```

![image-20220422142854106](../../../../../Library/Application%20Support/typora-user-images/image-20220422142854106.png)

lastlog

last

who

