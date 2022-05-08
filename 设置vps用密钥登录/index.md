# 

# 设置VPS用密钥登录

1. 生成rsa密钥对

    ```javascript
    cd ~ # 进入当前用户的工作目录
    mkdir .ssh # 创建.ssh文件加
    chmod -R 700 .ssh # 修改权限
    ssh-keygen -t rsa # 生成秘钥 
    cd .ssh # 进入.ssh目录
    touch authorized_keys # 生成公钥文件
    cat id_rsa.pub >> authorized_keys # 将之前生成的公钥内容输出到公钥文件中
    chmod 600 authorized_keys # 修改权限
    ```

    之后将id_rsa文件保存到本地电脑中，可以复制粘贴里面内容也可直接保存文件。

2. 修改系统配置

    ```
    vim /etc/ssh/sshd_config
    修改
    RSAAuthentication yes
    StrictModes no
    PubkeyAuthentication yes
    AuthorizedKeysFile .ssh/authorized_keys
    
    ```

    之后保存，关闭ssh会话，再用密钥登录，正常登录的话再禁止密码登录

    ```
    vim /etc/ssh/sshd_config
    PasswordAuthentication no # 禁止密码登录
    ```

    
