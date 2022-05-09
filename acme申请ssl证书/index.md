# Acme申请ssl证书


## acme申请SSL证书

## 说明

按照[官方的文档](https://github.com/acmesh-official/acme.sh/wiki/%E8%AF%B4%E6%98%8E) 中的通过添加域名解析API的方法操作了n次，一直不成功，索性用简单的standalone方法来申请吧。

## 准备条件

1. 准备好域名
2. 域名解析到VPS上
3. VPS的80端口开放

## 步骤

### 1. 安装acme.sh

`curl  https://get.acme.sh | sh -s email=my@example.com`
my@example.com替换成自己邮箱，随意填写应该也没问题，不要求验证。
如官方文档中说的，此命令会执行三个操作

1. 把 acme.sh 安装到你的 **home** 目录下`~/.acme.sh`
2. 创建 一个 bash 的 alias, 方便你的使用: `alias acme.sh=~/.acme.sh/acme.sh`
3. 自动为你创建 cronjob, 每天 0:00 点自动检测所有的证书, 如果快过期了, 需要更新, 则会自动更新证书.

### 2. 生成证书

如果你还没有运行任何 web 服务, **80** 端口是空闲的, 那么 **acme.sh** 还能假装自己是一个webserver, 临时听在**80** 端口, 完成验证:
`acme.sh  --issue -d mydomain.com   --standalone`
mydomain.com替换为自己的域名

当命令行有下图所示输出，表示成功了
![20220328-2005-99pSd3|400](https://cdn.jsdelivr.net/gh/ZevaXu/picupload@master/uPic/20220328-2005-99pSd3.png)

### 3. 安装证书

前面证书生成以后, 接下来需要把证书 copy 到真正需要用它的地方.

注意, 默认生成的证书都放在安装目录下: `~/.acme.sh/`, 请不要直接使用此目录下的文件, 例如: 不要直接让 nginx/apache 的配置文件使用这下面的文件. 这里面的文件都是内部使用, 而且目录结构可能会变化.

正确的使用方法是使用 `--install-cert` 命令,并指定目标位置, 然后证书文件会被copy到相应的位置, 例如:

#### Apache example:

```
acme.sh --install-cert -d example.com \
--cert-file      /path/to/certfile/in/apache/cert.pem  \
--key-file       /path/to/keyfile/in/apache/key.pem  \
--fullchain-file /path/to/fullchain/certfile/apache/fullchain.pem \
--reloadcmd     "service apache2 force-reload"
```

#### Nginx example:

```
acme.sh --install-cert -d example.com \
--key-file       /path/to/keyfile/in/nginx/key.pem  \
--fullchain-file /path/to/fullchain/nginx/cert.pem \
--reloadcmd     "service nginx force-reload"
```

(一个小提醒, 这里用的是 `service nginx force-reload`, 不是 `service nginx reload`, 据测试, `reload` 并不会重新加载证书, 所以用的 `force-reload`)

Nginx 的配置 `ssl_certificate` 使用 `/etc/nginx/ssl/fullchain.cer` ，而非 `/etc/nginx/ssl/<domain>.cer` ，否则 [SSL Labs](https://www.ssllabs.com/ssltest/) 的测试会报 `Chain issues Incomplete` 错误。

`--install-cert`命令可以携带很多参数, 来指定目标文件. 并且可以指定 reloadcmd, 当证书更新以后, reloadcmd会被自动调用,让服务器生效.

详细参数请参考: https://github.com/Neilpang/acme.sh#3-install-the-issued-cert-to-apachenginx-etc

值得注意的是, 这里指定的所有参数都会被自动记录下来, 并在将来证书自动更新以后, 被再次自动调用.

### 4. 更新证书

目前证书在 60 天以后会自动更新, 你无需任何操作. 今后有可能会缩短这个时间, 不过都是自动的, 你不用关心.

### 5. 更新 acme.sh

目前由于 acme 协议和 letsencrypt CA 都在频繁的更新, 因此 acme.sh 也经常更新以保持同步.

升级 acme.sh 到最新版 :

```
acme.sh --upgrade
```

如果你不想手动升级, 可以开启自动升级:

```
acme.sh  --upgrade  --auto-upgrade
```

之后, acme.sh 就会自动保持更新了.

你也可以随时关闭自动更新:

```
acme.sh --upgrade  --auto-upgrade  0
```

### 6. 出错怎么办

如果出错, 请添加 debug log：

```
acme.sh  --issue  .....  --debug 
```

或者：

```
acme.sh  --issue  .....  --debug  2
```

请参考： https://github.com/Neilpang/acme.sh/wiki/How-to-debug-acme.sh

最后, 本文并非完全的使用说明, 还有很多高级的功能, 更高级的用法请参看其他 wiki 页面.

https://github.com/Neilpang/acme.sh/wiki

