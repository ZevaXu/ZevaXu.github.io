# Shell语言基础


## 变量

### read

从键盘输入读取变量

```
read [-pt] variable
-p 提示
-t 时间

read -p "Please keyin your name: " -t 30 named
```

​		![20220421-1340-gS2fGF](https://cdn.jsdelivr.net/gh/ZevaXu/picupload@master/uPic/20220421-1340-gS2fGF.png)



### declare / typeset

宣告变量的类型

```
declare [-aixr] variable
-a 数组类型
-i 整数类型
-x 环境变量类型
-r 设置为只读类型
默认类型为字符串

declare -i sum=100+200+300
echo ${sum}
```

![20220421-1346-kNmqlf](https://cdn.jsdelivr.net/gh/ZevaXu/picupload@master/uPic/20220421-1346-kNmqlf.png)

* 默认类型为字符串
* 只能计算整数类型（1/3=0）



## 命令别名

### alias

给指令设置别名，新创一个新的指令， 你可以直接下达该指令

```
alias lm='ls -al | more'

```



### unalias

取消别名

```
unalias lm
```



## 历史命令 history

### history

查询历史命令

```
history n [-acrw]
n 数字，列出最近n条记录
-a 将目前新增的命令写入文件中
-c 清除目前shell中所有的history内容
-r 读取histfiles的内容
-w 写入到histfiles
```

## 路径与指令搜索顺序

1. 以相对/绝对路径执行指令，例如“ /bin/ls ”或“ ./ls ”；
2. 由 alias 找到该指令来执行；
3. 由 bash 内置的 （builtin） 指令来执行；
4. 通过 $PATH 这个变量的顺序搜寻到的第一个指令来执行。

```
type -a <指令>
查看指令的搜索顺序
```

<img src="https://cdn.jsdelivr.net/gh/ZevaXu/picupload@master/uPic/20220421-1432-DA7mnz.png" alt="20220421-1432-DA7mnz" style="zoom: 67%;" />

## bash的环境配置文件

**login shell会读取的配置文件**

1. /etc/profile :这是系统整体的设置，你最好不要修改这个文件；
2. ~/.bash_profile 或 ~/.bash_login 或 ~/.profile：属于使用者个人设置，你要改自己的数据，就写入这里！

![20220421-1444-7SpfVK](https://cdn.jsdelivr.net/gh/ZevaXu/picupload@master/uPic/20220421-1444-7SpfVK.png)

**source**

读取环境配置文件的指令



## 组合键、通用字符和特殊符号

**默认组合键**

![20220421-1456-UjKNar](https://cdn.jsdelivr.net/gh/ZevaXu/picupload@master/uPic/20220421-1456-UjKNar.png)

**万用字符 （wildcard）**

![20220421-1457-Q1aqcA](https://cdn.jsdelivr.net/gh/ZevaXu/picupload@master/uPic/20220421-1457-Q1aqcA.png)

使用例子：

![20220421-1459-pyG90F](https://cdn.jsdelivr.net/gh/ZevaXu/picupload@master/uPic/20220421-1459-pyG90F.png)

**特殊符号**

![20220421-1508-BOD7VW](https://cdn.jsdelivr.net/gh/ZevaXu/picupload@master/uPic/20220421-1508-BOD7VW.png)



## 同时执行多条命令

**1. cmd;cmd**

不考虑指令之间的相关性

**2. cmd&&cmd 和 cmd||cmd **

指令之间存在相关性

![20220421-1514-aFFiUX](https://cdn.jsdelivr.net/gh/ZevaXu/picupload@master/uPic/20220421-1514-aFFiUX.png)

`$?`表示指令执行的回传值，0表示成功，非0表示执行不成功



## 管线命令

管线命令可以对前一个命令执行后的输出标准进行处理和加工，产生用户最想要看到的格式。

<img src="https://cdn.jsdelivr.net/gh/ZevaXu/picupload@master/uPic/20220421-1545-3T2MQ8.png" alt="20220421-1545-3T2MQ8" style="zoom:67%;" />

### 截取命令

**cut**

将一行的信息按要求切割开，取一部分

```
cut [-dfc]
-d 接字符，表示以该字符切割
-f 接数字，与-d配合使用，表示取第几段
-c 接数字范围，表示取该范围内的字符

echo ${PATH} | cut -d ':' -f 5
echo ${PATH} | cut -c 5-19
```

![20220421-1553-YeVAb9](https://cdn.jsdelivr.net/gh/ZevaXu/picupload@master/uPic/20220421-1553-YeVAb9.png)



**grep**

分析一行信息，若当中有需要的信息，将该行取出

```
grep [-acinv] [--color=auto] '搜索字符' filename

-a 将二进制文件以text文件搜索
-c 计算共搜索到多少个满足要求的
-i 忽略大小写
-n 输出行号
-v 反向选择
--color=auto 加上颜色

last | grep 'root' #last 显示登录者信息

```

![image-20220421160817649](../../../../../Library/Application%20Support/typora-user-images/image-20220421160817649.png)



**sort**

排序

```
last | sort
```



**uniq**

重复的数据只取一行显示



**wc**

统计文件里有多少字符、多少行

```
wc [-lwm]
-l 列出行数
-w 列出多少英文字符
-m 多少字符

last | sort | uniq | wc -l
```





