# 

## git config 

Git 自带一个 git config 的工具来帮助设置控制 Git 外观和行为的配置变量。 这些变量存储在一个配置文件中。

### 查看文件位置以及所有的配置：

```
git config --list --show-origin

> /opt/homebrew/etc/
```

### 用户信息

```
git config --global user.name "John Doe"
git config --global user.email johndoe@example.com
```

### 查看配置信息

```
git config --list(-l)
```

## 获取git仓库

通常有两种获取 Git 项目仓库的方式：
1. 将尚未进行版本控制的本地目录转换为 Git 仓库；
2. 从其它服务器 克隆 一个已存在的 Git 仓库。

两种方式都会在你的本地机器上得到一个工作就绪的 Git 仓库。

### 本地目录转换为 Git 仓库

```
cd project_path # 空文件夹

git init. # 会自动创建一个 .git 目录（git仓库的主干）

git add *.c
git add LICNESE
git commit -m '初次提交'
```

![20220503-1206-fHpSv6](https://cdn.jsdelivr.net/gh/ZevaXu/picupload@master/uPic/20220503-1206-fHpSv6.png)

### 克隆现有的仓库

```
git clone https://github.com/libgit2/libgit2
```

这会在当前目录下创建一个名为 “libgit2” 的目录，并在这个目录下初始化一个 .git 文件夹， 从远程仓库拉取下所有数据放入 .git 文件夹，然后从中读取最新版本的文件的拷贝。

 Git 支持多种数据传输协议。 上面的例子使用的是 https:// 协议，不过你也可以使用 git:// 协议或者使用SSH 传输协议，比如 user@server:path/to/repo.git 。



## 记录更新到仓库

工作目录下的每一个文件都不外乎这两种状态：已跟踪 或 未跟踪。

**已跟踪：**的文件是指那些被纳入了版本控制的文件，在上一次快照中有它们的记录，在工作一段时间后， 它们的状态可能是未修改、已修改或已放入暂存区。简而言之，已跟踪的文件就是 Git 已经知道的文件

**未跟踪：**工作目录中除已跟踪文件外的其它所有文件都属于未跟踪文件，它们既不存在于上次快照的记录中，也没有被放入暂存区。 初次克隆某个仓库的时候，工作目录中的所有文件都属于已跟踪文件，并处于未修改状态，因为 Git刚刚检出了它们， 而你尚未编辑过它们。

![20220503-1158-VoYlGM](https://cdn.jsdelivr.net/gh/ZevaXu/picupload@master/uPic/20220503-1158-VoYlGM.png)



### 查看当前文件状态

`git statues`

![20220503-1200-o9KkfD](https://cdn.jsdelivr.net/gh/ZevaXu/picupload@master/uPic/20220503-1200-o9KkfD.png)

这说明你现在的工作目录相当干净。换句话说，**所有已跟踪文件在上次提交后都未被更改过。** 

此外，上面的信息还表明，**当前目录下没有出现任何处于未跟踪状态的新文件**，否则 Git 会在这里列出来。

最后，**该命令还显示了当前所在分支**，并告诉你这个分支同远程服务器上对应的分支没有偏离。 现在，分支名是“stable”，这是默认的分支名。

### 跟踪新文件

```
git add <文件名>
```

自行创建一个文件后，`git status`查看git状态会显示`未跟踪的文件`。`git add <文件>`之后，则变成`待提交状态`。

<img src="https://cdn.jsdelivr.net/gh/ZevaXu/picupload@master/uPic/20220507-1118-CC11DC.png" alt="20220507-1118-CC11DC" style="zoom:50%;" />

只要在 Changes to be committed 这行下面的，就说明是已暂存状态。 如果此时提交，那么该文件在你运行 git add 时的版本将被留存在后续的历史记录中。

git add 命令使用文件或目录的路径作为参数；如果参数是目录的路径，该命令将递归地跟踪该目录下的所有文件。

`git add`只会暂存运行改命令时刻的文件内容，之后进行的修改并没有记录。

#### 状态简览

`git status -s(--short)`

<img src="https://cdn.jsdelivr.net/gh/ZevaXu/picupload@master/uPic/20220507-1130-3PslYX.png" alt="20220507-1130-3PslYX" style="zoom:50%;" />

新添加的未跟踪文件前面有 ?? 标记，新添加到暂存区中的文件前面有 A 标记，修改过的文件前面有 M 标记。

 输出中有两栏，左栏指明了暂存区的状态，右栏指明了工作区的状态。例如，上面的状态报告显示： README 文件在工作区已修改但尚未暂存，而 lib/simplegit.rb 文件已修改且已暂存。 Rakefile 文件已修改，暂存后又作了修改，因此该文件的修改中既有已暂存的部分，又有未暂存的部分。

#### 忽略文件

`.gitignore`

把不需要纳入git管理的文件列入其中即可。

格式规范：

```
* 所有空行或者以 # 开头的行都会被 Git 忽略。
* 可以使用标准的 glob 模式匹配，它会递归地应用在整个工作区中。
* 匹配模式可以以（/）开头防止递归。
* 匹配模式可以以（/）结尾指定目录。
* 要忽略指定模式以外的文件或目录，可以在模式前加上叹号（!）取反。
```

shell简化的正则表达式：

```
星号（*）匹配零个或多个任意字符；
[abc] 匹配任何一个列在方括号中的字符 （这个例子要么匹配一个 a，要么匹配一个 b，要么匹配一个 c）； 
问号（?）只匹配一个任意字符；
如果在方括号中使用短划线分隔两个字符，表示所有在这两个字符范围内的都可以匹配（比如 [0-9] 表示匹配所有 0 到 9 的数字）。
使用两个星号（**）表示匹配任意中间目录，比如 a/**/z 可以匹配 a/z 、 a/b/z 或 a/b/c/z 等。
```

例子：

```
# 忽略所有的 .a 文件
*.a
# 但跟踪所有的 lib.a，即便你在前面忽略了 .a 文件
!lib.a
# 只忽略当前目录下的 TODO 文件，而不忽略 subdir/TODO
/TODO
# 忽略任何目录下名为 build 的文件夹
build/
# 忽略 doc/notes.txt，但不忽略 doc/server/arch.txt
doc/*.txt
# 忽略 doc/ 目录及其所有子目录下的 .pdf 文件
doc/**/*.pdf
# https://github.com/github/gitignore 列出详细的针对不同语言的忽略列表
```



### 查看已暂存和未暂存的修改

`git diff`

git diff 本身只显示尚未暂存的改动，而不是自上次提交以来所做的所有改动。 

<img src="https://cdn.jsdelivr.net/gh/ZevaXu/picupload@master/uPic/20220507-1142-hTV1CP.png" alt="20220507-1142-hTV1CP" style="zoom:50%;" />

### 提交更新

`git commit`

每一次运行提交操作，都是对你项目作一次快照，以后可以回到这个状态，或者进行比较。

#### 跳过使用暂存区域

`git commit -a`

不想一个个`git add`也可以跳过这一步，就会自动把所有已经跟踪过的文件暂存起来一并提交。

### 移除文件

`git rm `

将文件从暂存区移除，再提交。

如果要删除之前修改过或已经放到暂存区的文件，则必须使用强制删除选项 -f（译注：即 force 的首字母）。



#### 将文件从git仓库删除

`git rm --cached`

文件依然保留在本地，但是git不会再跟踪。

### 移动文件

`git mv`

Git 并不显式跟踪文件移动操作。 如果在 Git 中重命名了某个文件，仓库中存储的元数据并不会体现出这是一次改名操作。

```
git mv 相当于三条命令

mv README.md README
git rm README.md
git add README
```



## 查看提交历史

`git log`

![20220507-1207-TXfKpv](https://cdn.jsdelivr.net/gh/ZevaXu/picupload@master/uPic/20220507-1207-TXfKpv.png)

不传入任何参数的默认情况下，git log 会按时间先后顺序列出所有的提交，最近的更新排在最上面。 正如你所看到的，这个命令会列出每个提交的 SHA-1 校验和、作者的名字和电子邮件地址、提交时间以及提交说明。

#### 常用选项

-p: 它会显示每次提交所引入的差异

-2: 限制输出2条记录

--stat: 列出一个总结性的信息。列出所有被修改过的文文件，多少文件被修改等。

--pretty: 以美观的形式展现，并可以自定义输出格式

## 撤销操作

`git commit --amend`

如果前一次提交没有提交完整，比如忘记add 某个文件，或者commit描述不太对。

```
git commit -m 'initial commit'
git add forgotten_file

git commit --amend
```

最后只有一个提交，后面的提交会覆盖头一次提交。



## 远程仓库的使用

### 查看远程仓库

```
git remote
-v 显示仓库对应的url
```

<img src="https://cdn.jsdelivr.net/gh/ZevaXu/picupload@master/uPic/20220508-1033-Bbd3ij.png" alt="20220508-1033-Bbd3ij" style="zoom:50%;" />

### 添加远程仓库

```
git clone url

git remote add <短命名> url
# 用pb代替url
git remote add pb https://github.com/paulboone/ticgit

```

### 从远程仓库拉取和抓取

```
git fetch <remote> # 从远程仓库获取本地没有的内容

git clone # 克隆了一个仓库，命令会自动将其添加为远程仓库并默认以 “origin” 为简
```

### 推送到远程仓库

```
git push <remote> <branch>
git push origin master
```

只有当你有所克隆服务器的写入权限，并且之前没有人推送过时，这条命令才能生效。

 当你和其他人在同一时间克隆，他们先推送到上游然后你再推送到上游，你的推送就会毫无疑问地被拒绝。 你必须先抓取他们的工作并将其合并进你的工作后才能推送。

### 查看远程仓库

```
git remote show <remotr>
git remote show origin
```

<img src="https://cdn.jsdelivr.net/gh/ZevaXu/picupload@master/uPic/20220508-1115-azQ46k.png" alt="20220508-1115-azQ46k" style="zoom:50%;" />

### 远程仓库重命名与移除

重命名

```
git remote rename <old_name> <new_name>
git remote rename pb paul
```

移除

```
git remote remove paul
```

一旦你使用这种方式删除了一个远程仓库，那么所有和这个远程仓库相关的远程跟踪分支以及配置信息也会一起被删除。

## 打标签

用于标记发布版本等

### 列出标签

```
git tag
-l 匹配
git tag -l "v1.8*"

```

### 创建附注标签

附注标签是存储在 Git 数据库中的一个完整对象， 它们是可以被校验的，其中包含打标签者的名字、电子邮件地址、日期时间， 此外还有一个标签信息。

```
git tag -a 
git tag -a v1.4 -m "my version 1.4"
-m 指定一条存储在标签中的信息

查看标签：
git show v1.4
```

### 创建轻量标签

轻量标签相比附注标签是一条更简单的标签，没有其他信息。

```
# 打标签
git tag v1.4
#查看标签
git show v1.4

```

### 后期标签

```
# 查看提交历史
git log --pretty=online

# 给历史提交打标签
git tag -a v1.2 9fceb02
```

### 共享标签

默认情况下，git push 命令并不会传送标签到远程仓库服务器上。 在创建完标签后你必须显式地推送标签到共享服务器上。

```
git push origin <tagname>
git push origin v1.5
```

### 删除标签

删除本地标签

```
git tag -d <tag_name>
git tag -d v1.4-lw

```

推送到远程

```
git push orgin --delete <tag_name>
# 或者
git push <remote> :refs/tags/<tagname> 
git push origin :refs/tags/v1.4-lw
```



## Git别名

为git命令创建别名

```
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.ci commit
git config --global alias.st status

git config --global alias.last 'log -1 HEAD'

```

 当要输入 git commit 时，只需要输入 git ci

