# Conda常用命令


# conda常用命令

## 1. 管理conda

1. 查看conda版本
   
   ```
   conda --version
   ```

2. 查看python版本
   
   ```
   conda --python
   ```

3. 进入/退出python环境
   
   ```
   python
   quit()
   ```

4. 更新conda版本
   
   ```
   conda update conda
   ```

---

## 2. 管理环境

1. 创建一个环境
   
   ```
   conda create --name python34 python=3.4 #创建一个名为python34的环境，指定python版本为3.4
   ```

2. 切换环境
   
   ```
   #windows
   activate python34
   #mac/linux
   source activate python34
   ```

3. 删除一个已有的环境
   
   ```
   conda remove --name python34 --all
   ```

4. 列出所有环境
   
   ```
   conda info -e
   #conda info -envs
   ```

5. 复制一个环境
   
   ```
   conda create -n flowers --clone python34
   ```

---

## 3. 管理包

- conda安装和管理python包非常方便，可以在指定的python环境中安装包，且自动安装所需要的依赖包，避免了很多拓展包冲突兼容问题。
- 不建议使用easy_install安装包。大部分包都可以使用conda安装，无法使用conda和anaconda.org安装的包可以通过pip命令安装
- 使用合适的源可以提升安装的速度

### 3.1 使用conda命令安装

1. 查看该环境中已经安装的包
   
   ```
   conda list
   ```

2. 在指定环境中安装指定包
   
   ```
   #1.直接指定
   conda install --name python34 beautifulsoup4
   
   #2. 进入指定环境再安装
   source activate python34 #windou s系统去除 source
   ```

### 3.2 从Anaconda.org安装一个包

如果一个包不能使用conda安装，我们接下来将在Anaconda.org网站查找。

在浏览器中，去 [Anaconda资源官网](http://anaconda.org/)。我们查找一个叫“bottleneck”的包，所以在左上角的叫“Search Anaconda Cloud”搜索框中输入“bottleneck”并点击search按钮。

Anaconda.org上会有超过一打的bottleneck包的版本可用，但是我们想要那个被下载最频繁的版本。所以你可以通过下载量来排序，通过点击Download栏。
点击包的名字来选择最常被下载的包。它会链接到Anaconda.org详情页显示下载的具体命令：

```
conda install--channel https：//conda .anaconda.ort/pandas bottleneck
```

### 3.3 pip命令安装

- 对于那些无法通过conda安装或者从Anaconda.org获得的包，我们通常可以用pip命令来安装包。

- pip只是一个包管理器，所以它不能为你管理环境。pip甚至不能升级python，因为它不像conda一样把python当做包来处理。但是它可以安装一些conda安装不了的包。

- 可以上[pypi网站](https://pypi.python.org/)查询要安装的包，查好以后输入pip install命令就可以安装这个包了。

我们激活想要放置程序的python环境，然后通过pip安装一个叫“See”的程序。

```
pip install see
```

### 3.4 文件源码安装

如果真的遇到走投无路的境地，也就是上面这些方法通通不管用！！！那就只能下载源码安装了，比如exe文件（双击安装）或者whl文件（pip安装）等等。还有在github上找到源码，使用`python setup.py install`命令安装。

## 3.5 移除

1. 移除一个包
   
   ```
   conda remove -n beautifulsoup4
   ```

2. 移除环境
   
   ```
   conda remove -n python34 --all
   ```

3. 移除conda
   
   ```
   #linux
   rm -rf ~/miniconda
   #或者
   rm -rf ~/anaconda
   #或者
   ```

---

# 4. 频道管理

1. 列出所有channel
   
   ```
   conda config --show channels
   #或者
   conda config --get channels
   ```

2. 追加channel
   
   ```
   conda config --append channels CHANNEL_NAME #并没有改变优先级
   ```

3. 设定首要channle
   
   ```
   conda config add channels conda-forge #将此channel设定为第一优先级
   ```

4. 删除channel
   
   ```
   conda config --remove channels NOT_WANTED
   ```

5. 添加清华源
   
   ```
   conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/
   conda config --set show_channel_urls yes
   ```

6. 源
   
   ```
   #豆瓣
   https://pypi.douban.com/simple/
   #默认源
   https://pypi.org/simple
   ```

