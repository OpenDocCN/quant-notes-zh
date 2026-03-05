# Python数据分析实战：P4：03：安装Python解释器 🐍

在本节课中，我们将学习如何安装Python编程环境，即Python解释器。这是开始编写和运行Python代码的第一步。

## 为何需要安装Python环境

上一节我们介绍了Python在数据分析中的应用，本节中我们来看看如何搭建运行环境。要运行Python代码，必须安装Python解释器。

人类编写的Python代码（源码）是易于理解的文本，但计算机无法直接执行。计算机只能理解由0和1组成的机器码（二进制）。Python解释器的作用就是充当“翻译官”，将人类编写的源码转换为计算机能执行的机器码，并将执行结果转换回人类可读的形式。

这个过程可以概括为：
**人类源码 -> Python解释器 -> 机器码 -> 执行结果 -> Python解释器 -> 人类可读结果**

![](img/33d54f29d42f5da7fb6b8539f14c30b3_1.png)

![](img/33d54f29d42f5da7fb6b8539f14c30b3_3.png)

因此，安装Python解释器是运行Python程序的必要条件。

## Windows系统安装步骤

![](img/33d54f29d42f5da7fb6b8539f14c30b3_5.png)

以下是Windows系统安装Python的详细步骤。

1.  **访问官网下载**
    打开浏览器，访问Python官方网站 `python.org`。点击导航栏的“Downloads”选项，选择“Windows”操作系统。

![](img/33d54f29d42f5da7fb6b8539f14c30b3_7.png)

2.  **选择安装版本**
    页面会列出所有可用于Windows的Python版本。请选择Python 3.x系列的最新版本（如Python 3.8或更高）。Python 2系列已停止维护，不应再使用。

![](img/33d54f29d42f5da7fb6b8539f14c30b3_9.png)

3.  **选择安装文件**
    在文件列表中找到可执行安装程序。对于大多数现代电脑（内存大于4G），应选择标注为“Windows x86-64 executable installer”的文件进行下载。这表示64位安装程序。

4.  **运行安装程序**
    下载完成后，双击运行安装程序。在安装向导中，务必勾选“Add Python 3.x to PATH”选项。这个步骤将Python添加到系统环境变量，允许你在任何目录下通过命令行调用Python。

![](img/33d54f29d42f5da7fb6b8539f14c30b3_11.png)

![](img/33d54f29d42f5da7fb6b8539f14c30b3_13.png)

![](img/33d54f29d42f5da7fb6b8539f14c30b3_15.png)

5.  **完成安装**
    点击“Install Now”开始安装。安装完成后，关闭安装程序。

![](img/33d54f29d42f5da7fb6b8539f14c30b3_17.png)

![](img/33d54f29d42f5da7fb6b8539f14c30b3_19.png)

![](img/33d54f29d42f5da7fb6b8539f14c30b3_21.png)

6.  **验证安装**
    按下键盘上的 `Win + R` 键，输入 `cmd` 并回车，打开命令提示符（一个黑色窗口）。在窗口中输入 `python` 并回车。
    如果安装成功，你将看到类似 `Python 3.x.x ... >>>` 的提示符，这表示已进入Python交互式环境。输入 `exit()` 可以退出该环境。

![](img/33d54f29d42f5da7fb6b8539f14c30b3_23.png)

![](img/33d54f29d42f5da7fb6b8539f14c30b3_25.png)

## Mac系统安装步骤

![](img/33d54f29d42f5da7fb6b8539f14c30b3_27.png)

Mac系统的安装过程更为简单。

![](img/33d54f29d42f5da7fb6b8539f14c30b3_29.png)

![](img/33d54f29d42f5da7fb6b8539f14c30b3_31.png)

![](img/33d54f29d42f5da7fb6b8539f14c30b3_33.png)

1.  **下载安装包**
    同样访问Python官网 `python.org`，在“Downloads”下选择“macOS”，下载最新的Python 3.x安装包（.pkg文件）。

![](img/33d54f29d42f5da7fb6b8539f14c30b3_35.png)

![](img/33d54f29d42f5da7fb6b8539f14c30b3_37.png)

2.  **运行安装**
    双击下载的.pkg文件，按照图形化向导提示完成安装，通常无需额外配置。

![](img/33d54f29d42f5da7fb6b8539f14c30b3_39.png)

![](img/33d54f29d42f5da7fb6b8539f14c30b3_41.png)

3.  **验证安装**
    打开“启动台”(Launchpad)，在搜索框中输入“ter”找到“终端”(Terminal)并打开。在终端中输入 `python3` 并回车。
    如果看到 `Python 3.x.x ... >>>` 的提示符，则说明安装成功。Mac系统可能预装了Python 2.7，因此测试时请使用 `python3` 命令。输入 `exit()` 退出。

## 安装成功确认

![](img/33d54f29d42f5da7fb6b8539f14c30b3_43.png)

无论使用哪种系统，安装成功后，你都可以通过命令行工具（Windows的CMD或Mac的终端）输入 `python`（或 `python3`）进入交互式环境。这是一个可以立即执行Python代码的工具，我们将在后续课程中使用它。

![](img/33d54f29d42f5da7fb6b8539f14c30b3_45.png)

本节课中我们一起学习了安装Python解释器的原因和具体步骤。你已经成功搭建了Python编程环境，为后续的代码编写和数据分析项目打下了基础。下一节，我们将在这个环境中写下你的第一行Python代码。