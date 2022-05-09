# Linux软链接和硬链接

## Hard link

实体链接、硬链接

### 预备知识

* 每个文件都会占用一个 inode ，文件内容由 inode 的记录来指向；
* 想要读取该文件，必须要经过目录记录的文件名来指向到正确的 inode 号码才能读取。
* 文件名只与目录有关，但是文件内容则与 inode 有关。

### 原理

hard link是在某个目录下新增一个文件名，连接到某个inode号码。

```
ll -i [文件名] # 显示文件的inode号码
```

![20220410-1010-0POsJq](https://cdn.jsdelivr.net/gh/ZevaXu/picupload@master/uPic/20220410-1010-0POsJq.png)

建立一个硬链接后，再次查看两个文件名的inode。

![20220410-1016-GDABgY](https://cdn.jsdelivr.net/gh/ZevaXu/picupload@master/uPic/20220410-1016-GDABgY.png)

两个是一样的，权限字段后的数字2表示有多少个文件名链接到此inode。

![20220410-1019-huiQ7i](https://cdn.jsdelivr.net/gh/ZevaXu/picupload@master/uPic/20220410-1019-huiQ7i.png)

## 好处

如果你将任何一个“文件名”删除，其实 inode 与 block 都还是存在的！ 此时你可以通过另一个“文件名”来读取到正确的文件数据喔！此外，不论你使用哪个“文件名”来编辑， 最终的结果都会写入到相同的 inode 与 block 中，因此均能进行数据的修改。

### 限制

* 不支持为目录创建hard link
* 不能跨文件系统

## Symbolic link

符号链接、软链接

Symbolic link 就是在创建一个独立的文件，而这个文件会让数据的读取指向他 link 的那个文件的文件名！由于只是利用文件来做为指向的动作， 所以，当来源文件被删除之后，symbolic link 的文件会“开不了”，也就是找不到原始的文件名。

![20220410-1022-QvnXQh](https://cdn.jsdelivr.net/gh/ZevaXu/picupload@master/uPic/20220410-1022-QvnXQh.png)

### 实操

![20220410-1025-j36uRY](https://cdn.jsdelivr.net/gh/ZevaXu/picupload@master/uPic/20220410-1025-j36uRY.png)

删除之前创建的硬链接，再新建一个软链接，可以看到，实际上是新建立的一个新的文件，拥有和原始文件不同的inode。

该文件大小是7bytes，是因为箭头右边的文件名字符数量是7个。

这个 Symbolic Link 与 Windows 的捷径可以给他划上等号，由 Symbolic link所创建的文件为一个独立的新的文件，所以会占用掉 inode 与 block 。

