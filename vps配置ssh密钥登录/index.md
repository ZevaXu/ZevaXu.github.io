# VPS配置SSH密钥登录

## 基本配置

```
#新建一个user
sudo useradd -d /home/xzh xzh

# 为其设置密码
passwd xzh

# 配置基本配置文件（环境变量等）
cp ./bashrc /home/xzh/.bashrc
cp ./bash_profile /home/xzh/.bash_profile

# 改变 配置文件属性
chown xzh /home/xzh/.bashrc
chmod 760 /home/xzh/.bash_profile

# 自定义$PS1变量（每条命令前面显示的格式）
PS1="\[\e[37;40m\][\[\e[32;40m\]\u\[\e[37;40m\]@\h \[\e[36;40m\]\w\[\e[0m\]]\\$ "
追加到.bashrc中

# 切换到xzh
su xzh

```

## 通过密码登录VPS

```
# 通过ssh登录到主机
ssh xzh@ip 
>> 输入密码
```



## 配置ssh_key登录VPS

```
ssh-keygen -t rsa # 生成一对 rsa 私钥和公钥在 ～/.ssh文件夹下
```

`id_rsa`: 私钥

`id_rsa.pub`: 公钥

此时这对密钥是没有用的，但是一旦把`id_rsa.pub`里的内容复制VPS的`authorized_keys`这个文件中，那么就可以用其对应的私钥`id_rsa`进行远程登录该机器。

```
# 将id_rsa.pub里的内容追加到authorized_keys,最好复制两次一样的
cat id_rsa.pub >> authorized_keys #x2
# 或者
echo <公钥内容> >> authorized_keys #x2

# 设置适当的权限
chmod 700 .ssh 
chmod 600 authorized_keys
```

将`id_rsa`文件下载到本机（建立文本文件，复制进去）

```
# 此时，可以指定私钥来登录到VPS中
ssh -i <path_to_id_rsa> xzh@IP 
```

## ssh登录配置文件

主要配置在` /etc/ssh/sshd_config`文件中

```
RSAAuthentication yes # 开启密钥登入的认证方式
PubkeyAuthentication yes # 开启密钥登入的认证方式
PermitRootLogin yes # 此处请留意 root 用户能否通过 SSH 登录，默认为yes（注：可以正常用密钥登录了，再把这里改为no）
PasswordAuthentication yes #当我们完成全部设置并以密钥方式登录成功后，可以禁用密码登录。这里我们先不禁用，先允许密码登陆
```





