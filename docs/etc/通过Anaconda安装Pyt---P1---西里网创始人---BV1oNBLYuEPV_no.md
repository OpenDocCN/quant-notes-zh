# Python环境搭建：P1：在Linux服务器上通过Anaconda安装Python3与Jupyter Notebook

![](img/688c74cffd3eb90db50f15fa0a603265_1.png)

![](img/688c74cffd3eb90db50f15fa0a603265_3.png)

![](img/688c74cffd3eb90db50f15fa0a603265_5.png)

![](img/688c74cffd3eb90db50f15fa0a603265_7.png)

![](img/688c74cffd3eb90db50f15fa0a603265_9.png)

![](img/688c74cffd3eb90db50f15fa0a603265_11.png)

在本节课中，我们将学习如何在Linux远程服务器上，通过Anaconda来安装Python3环境，并配置和启动Jupyter Notebook。这是一种高效管理Python项目依赖和环境的方式，尤其适合数据分析和量化交易等领域。

![](img/688c74cffd3eb90db50f15fa0a603265_13.png)

![](img/688c74cffd3eb90db50f15fa0a603265_15.png)

## 准备工作 🛠️

![](img/688c74cffd3eb90db50f15fa0a603265_17.png)

![](img/688c74cffd3eb90db50f15fa0a603265_19.png)

![](img/688c74cffd3eb90db50f15fa0a603265_20.png)

在开始安装之前，需要完成一些准备工作。上一节我们介绍了课程目标，本节中我们来看看具体需要准备什么。

![](img/688c74cffd3eb90db50f15fa0a603265_22.png)

你需要拥有一台可以访问的Linux远程服务器（例如云服务器），并确保你拥有该服务器的管理员权限（或使用`sudo`命令的权限）。

以下是准备工作清单：
*   购买或准备一台Linux远程服务器。
*   确保服务器的防火墙设置允许后续访问Jupyter Notebook的端口（默认为8888）。
*   准备一个SSH客户端（如Xshell、MobaXterm或系统自带的终端）来连接服务器。

![](img/688c74cffd3eb90db50f15fa0a603265_24.png)

## 下载Anaconda安装脚本 📥

![](img/688c74cffd3eb90db50f15fa0a603265_26.png)

![](img/688c74cffd3eb90db50f15fa0a603265_28.png)

![](img/688c74cffd3eb90db50f15fa0a603265_30.png)

![](img/688c74cffd3eb90db50f15fa0a603265_32.png)

准备工作完成后，我们需要获取Anaconda的安装脚本。Anaconda是一个集成了Python和众多科学计算库的发行版，能极大简化环境配置。

![](img/688c74cffd3eb90db50f15fa0a603265_34.png)

![](img/688c74cffd3eb90db50f15fa0a603265_36.png)

![](img/688c74cffd3eb90db50f15fa0a603265_38.png)

![](img/688c74cffd3eb90db50f15fa0a603265_40.png)

![](img/688c74cffd3eb90db50f15fa0a603265_42.png)

我们将在服务器的命令行中，使用`wget`命令下载Anaconda的安装脚本。你可以访问Anaconda官方文档获取最新的脚本下载链接。

![](img/688c74cffd3eb90db50f15fa0a603265_44.png)

![](img/688c74cffd3eb90db50f15fa0a603265_46.png)

![](img/688c74cffd3eb90db50f15fa0a603265_48.png)

![](img/688c74cffd3eb90db50f15fa0a603265_50.png)

在服务器终端中执行以下命令：
```bash
wget https://repo.anaconda.com/archive/Anaconda3-2023.03-1-Linux-x86_64.sh
```
此命令会下载指定版本的Anaconda安装脚本。请注意，版本号可能会更新，请以官网最新版本为准。

![](img/688c74cffd3eb90db50f15fa0a603265_52.png)

![](img/688c74cffd3eb90db50f15fa0a603265_54.png)

## 安装Anaconda ⚙️

![](img/688c74cffd3eb90db50f15fa0a603265_56.png)

![](img/688c74cffd3eb90db50f15fa0a603265_58.png)

![](img/688c74cffd3eb90db50f15fa0a603265_60.png)

下载完成后，就可以运行安装脚本了。这个过程会在你的服务器上安装Python和conda环境管理工具。

![](img/688c74cffd3eb90db50f15fa0a603265_62.png)

![](img/688c74cffd3eb90db50f15fa0a603265_64.png)

运行以下命令开始安装：
```bash
bash Anaconda3-2023.03-1-Linux-x86_64.sh
```
安装过程中，请注意以下步骤：
1.  阅读许可协议，并按提示输入`yes`同意。
2.  确认安装路径，通常直接按回车使用默认路径即可。
3.  等待安装程序解压和安装文件。

![](img/688c74cffd3eb90db50f15fa0a603265_66.png)

![](img/688c74cffd3eb90db50f15fa0a603265_67.png)

![](img/688c74cffd3eb90db50f15fa0a603265_69.png)

![](img/688c74cffd3eb90db50f15fa0a603265_71.png)

![](img/688c74cffd3eb90db50f15fa0a603265_73.png)

安装最后，安装程序会询问是否初始化conda，通常建议输入`yes`。这会将conda添加到你的系统环境变量中。

![](img/688c74cffd3eb90db50f15fa0a603265_75.png)

![](img/688c74cffd3eb90db50f15fa0a603265_77.png)

## 激活并配置Conda环境 🔧

![](img/688c74cffd3eb90db50f15fa0a603265_79.png)

![](img/688c74cffd3eb90db50f15fa0a603265_81.png)

![](img/688c74cffd3eb90db50f15fa0a603265_83.png)

![](img/688c74cffd3eb90db50f15fa0a603265_85.png)

安装完成后，需要激活conda环境，使其命令生效。然后，我们将创建一个独立的Python环境用于项目，这是一个良好的实践。

![](img/688c74cffd3eb90db50f15fa0a603265_87.png)

![](img/688c74cffd3eb90db50f15fa0a603265_89.png)

![](img/688c74cffd3eb90db50f15fa0a603265_91.png)

首先，关闭并重新打开终端，或者运行以下命令激活conda：
```bash
source ~/.bashrc
```
激活后，你可以使用以下命令创建一个新的Python环境（例如命名为`my_env`）：
```bash
conda create -n my_env python=3.9
```
创建完成后，激活这个环境：
```bash
conda activate my_env
```
现在，你的命令行提示符前应该会出现`(my_env)`，表示你正在该环境中工作。

![](img/688c74cffd3eb90db50f15fa0a603265_93.png)

![](img/688c74cffd3eb90db50f15fa0a603265_95.png)

![](img/688c74cffd3eb90db50f15fa0a603265_97.png)

![](img/688c74cffd3eb90db50f15fa0a603265_99.png)

![](img/688c74cffd3eb90db50f15fa0a603265_101.png)

## 配置Jupyter Notebook以支持远程访问 🌐

![](img/688c74cffd3eb90db50f15fa0a603265_102.png)

![](img/688c74cffd3eb90db50f15fa0a603265_104.png)

![](img/688c74cffd3eb90db50f15fa0a603265_106.png)

![](img/688c74cffd3eb90db50f15fa0a603265_108.png)

![](img/688c74cffd3eb90db50f15fa0a603265_110.png)

默认情况下，Jupyter Notebook只允许本地访问。为了能在我们自己的电脑上通过浏览器访问服务器上的Jupyter，需要进行远程访问配置。

![](img/688c74cffd3eb90db50f15fa0a603265_112.png)

![](img/688c74cffd3eb90db50f15fa0a603265_114.png)

首先，在当前的conda环境中安装Jupyter Notebook：
```bash
conda install jupyter
```
接下来，生成Jupyter的配置文件：
```bash
jupyter notebook --generate-config
```
生成的配置文件通常位于`~/.jupyter/jupyter_notebook_config.py`。我们需要修改它。

![](img/688c74cffd3eb90db50f15fa0a603265_116.png)

![](img/688c74cffd3eb90db50f15fa0a603265_118.png)

以下是需要修改的关键配置项，你可以使用`vim`或`nano`等文本编辑器打开并修改配置文件：
```python
# 允许所有IP访问
c.NotebookApp.allow_origin = ‘*‘
# 禁止启动时自动打开浏览器（服务器无图形界面）
c.NotebookApp.open_browser = False
# 设置访问端口，默认为8888
c.NotebookApp.port = 8888
# 设置允许远程访问
c.NotebookApp.allow_remote_access = True
# 设置一个访问密码（可选，但建议设置）
# 首先在Python中生成密码哈希：`from notebook.auth import passwd; passwd()`
# 然后将输出的哈希字符串复制到这里
# c.NotebookApp.password = ‘sha1:你的密码哈希值‘
```

## 启动Jupyter Notebook 🚀

![](img/688c74cffd3eb90db50f15fa0a603265_120.png)

![](img/688c74cffd3eb90db50f15fa0a603265_122.png)

![](img/688c74cffd3eb90db50f15fa0a603265_124.png)

![](img/688c74cffd3eb90db50f15fa0a603265_126.png)

![](img/688c74cffd3eb90db50f15fa0a603265_128.png)

配置完成后，就可以启动Jupyter Notebook服务了。由于是在服务器后台运行，我们使用`nohup`命令使其在断开SSH连接后也能持续运行。

![](img/688c74cffd3eb90db50f15fa0a603265_129.png)

![](img/688c74cffd3eb90db50f15fa0a603265_131.png)

![](img/688c74cffd3eb90db50f15fa0a603265_132.png)

![](img/688c74cffd3eb90db50f15fa0a603265_133.png)

![](img/688c74cffd3eb90db50f15fa0a603265_134.png)

![](img/688c74cffd3eb90db50f15fa0a603265_135.png)

在终端中执行：
```bash
nohup jupyter notebook &
```
命令会返回一个进程ID，并开始运行。你可以使用`jupyter notebook list`命令查看正在运行的Notebook服务及其访问令牌。

![](img/688c74cffd3eb90db50f15fa0a603265_137.png)

![](img/688c74cffd3eb90db50f15fa0a603265_138.png)

![](img/688c74cffd3eb90db50f15fa0a603265_139.png)

![](img/688c74cffd3eb90db50f15fa0a603265_141.png)

## 本地访问与基本库安装 📊

![](img/688c74cffd3eb90db50f15fa0a603265_143.png)

![](img/688c74cffd3eb90db50f15fa0a603265_145.png)

![](img/688c74cffd3eb90db50f15fa0a603265_147.png)

服务启动后，在你的本地电脑浏览器中，输入`服务器IP地址:8888`（例如`123.123.123.123:8888`）进行访问。如果设置了密码，输入密码即可进入Jupyter Notebook界面。

![](img/688c74cffd3eb90db50f15fa0a603265_149.png)

![](img/688c74cffd3eb90db50f15fa0a603265_151.png)

![](img/688c74cffd3eb90db50f15fa0a603265_153.png)

进入后，你可以创建一个新的Python笔记本进行测试。对于量化分析等项目，通常需要安装一些核心库。

![](img/688c74cffd3eb90db50f15fa0a603265_155.png)

![](img/688c74cffd3eb90db50f15fa0a603265_156.png)

![](img/688c74cffd3eb90db50f15fa0a603265_157.png)

![](img/688c74cffd3eb90db50f15fa0a603265_159.png)

![](img/688c74cffd3eb90db50f15fa0a603265_161.png)

以下是几个常用的库，可以在Jupyter Notebook的代码单元格中使用`!pip install`命令安装：
```python
!pip install numpy pandas matplotlib
!pip install ta  # 技术分析库
!pip install backtrader  # 量化回测框架
```

![](img/688c74cffd3eb90db50f15fa0a603265_163.png)

![](img/688c74cffd3eb90db50f15fa0a603265_165.png)

![](img/688c74cffd3eb90db50f15fa0a603265_166.png)

![](img/688c74cffd3eb90db50f15fa0a603265_168.png)

## 进程管理与问题排查 🔍

![](img/688c74cffd3eb90db50f15fa0a603265_170.png)

![](img/688c74cffd3eb90db50f15fa0a603265_172.png)

如果需要停止Jupyter Notebook服务，可以先找到其进程ID，然后终止它。

![](img/688c74cffd3eb90db50f15fa0a603265_174.png)

![](img/688c74cffd3eb90db50f15fa0a603265_176.png)

![](img/688c74cffd3eb90db50f15fa0a603265_178.png)

首先，查找Jupyter相关进程：
```bash
ps aux | grep jupyter
```
找到对应的进程ID（PID）后，使用`kill`命令终止：
```bash
kill -9 <PID>
```
如果遇到无法访问的情况，请检查：
1.  服务器安全组/防火墙是否放行了`8888`端口。
2.  Jupyter服务是否在运行（`jupyter notebook list`）。
3.  配置文件中的IP和端口设置是否正确。

![](img/688c74cffd3eb90db50f15fa0a603265_180.png)

---

![](img/688c74cffd3eb90db50f15fa0a603265_182.png)

![](img/688c74cffd3eb90db50f15fa0a603265_183.png)

本节课中我们一起学习了在Linux服务器上通过Anaconda搭建Python3和Jupyter Notebook环境的完整流程。从准备工作、下载安装、环境配置到远程访问和基本库安装，我们完成了数据科学和量化分析中基础工作平台的搭建。掌握这个环境后，你就可以在强大的服务器上运行和管理你的Python项目了。