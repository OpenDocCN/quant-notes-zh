# 量化金融分析师.AQF：01：Python语言环境搭建 🐍

在本节课中，我们将学习如何为量化金融分析搭建Python编程环境。这是后续所有Python编程和数据分析工作的基础。

## 课程概述

Python编程语言在量化投资中应用非常广泛，特别是在中低频交易中。因其简单易学，受到广泛欢迎。本课程将介绍以下四个核心部分：
1.  **Python语言环境搭建**：学习如何安装Anaconda软件，建立编程环境。
2.  **Python编程基础**：讲解Python的基础语法、基本数据类型、控制结构、异常处理以及函数的定义和调用。
3.  **Python数据分析**：介绍两大核心数据分析模块NumPy和Pandas。在NumPy中，我们将学习`ndarray`数据类型；在Pandas中，我们将学习`Series`和`DataFrame`这两个数据类型。
4.  **Python数据可视化**：介绍如何使用Matplotlib库进行数据可视化，即用Python画图。

此外，课程附录还会讲解金融数量分析中常见的概率分布，并演示如何用Python绘制相关图形。

我们将从最基础的环境搭建开始。

## 安装Anaconda

我们之后所有的Python编程都将在Jupyter Notebook上运行。本节课的目标是教大家下载并运行Jupyter Notebook。我们需要安装的是Anaconda软件。

![](img/5ab0c9d5deb367c483ed0fd6b4c160ee_1.png)

**重要提示**：如果你的电脑上已经通过官网安装了Python，建议先将其卸载。因为电脑上存在多个Python版本会导致混乱，例如安装的第三方库可能无法在Anaconda环境中找到。你只需要安装Anaconda，它已内置了Python，无需单独安装。

### 下载Anaconda

我们可以到Anaconda官网下载。在官网的“Products”菜单下，选择“Individual Edition”，页面底部会提供下载链接。

官网提供了三个系统的安装版本：Windows、Mac OS和Linux。我们主要介绍前两种。

**版本选择**：
*   务必下载**Python 3.7**版本。Python 2已停止维护，我们学习的是Python 3。
*   **Windows系统**：首先查看你的电脑是32位还是64位操作系统（可通过“计算机”->右键“属性”查看）。根据系统位数，下载对应的64位或32位安装包。
*   **Mac OS系统**：建议下载图形界面安装包（通常为`.pkg`文件），便于操作。命令行安装包（`.sh`文件）对初学者可能不够友好。

### 安装步骤

下载安装包后，双击运行，按照提示操作即可。

**安装注意事项**：
1.  **安装路径**：建议使用默认安装路径。**路径中不要包含中文或空格**，否则可能报错。
2.  **勾选选项**：安装过程中出现的所有复选框建议**全部勾选**，特别是“Add Anaconda to my PATH environment variable”这一项，这可以避免后续手动配置环境变量的麻烦。
3.  **磁盘空间**：确保安装路径所在磁盘有至少3GB的剩余空间。

![](img/5ab0c9d5deb367c483ed0fd6b4c160ee_3.png)

安装完成后，你可以在开始菜单中找到“Anaconda Navigator”。

![](img/5ab0c9d5deb367c483ed0fd6b4c160ee_5.png)

![](img/5ab0c9d5deb367c483ed0fd6b4c160ee_7.png)

## 运行Jupyter Notebook

![](img/5ab0c9d5deb367c483ed0fd6b4c160ee_9.png)

安装Anaconda后，我们需要运行Jupyter Notebook。以下是不同系统的启动方法。

### Windows系统启动方法

![](img/5ab0c9d5deb367c483ed0fd6b4c160ee_11.png)

在Windows系统上，有三种常用方式可以打开Jupyter Notebook：
1.  **开始菜单直接运行**：在开始菜单中找到并点击“Jupyter Notebook”图标。
2.  **通过命令提示符运行**：打开“命令提示符”（CMD），输入 `jupyter notebook` 并回车。
3.  **通过Anaconda Prompt运行**：在开始菜单中找到并打开“Anaconda Prompt”，输入 `jupyter notebook` 并回车。

![](img/5ab0c9d5deb367c483ed0fd6b4c160ee_13.png)

以上三种方式任选其一即可。

### 在指定路径打开Jupyter Notebook（Windows）

如果你的代码文件保存在非系统盘（如D盘、E盘），直接在开始菜单运行Jupyter Notebook可能难以定位到你的文件夹。此时可以在指定文件夹路径下打开：

以下是操作步骤：
1.  进入你保存代码文件的文件夹（例如：`D:\新建文件夹`）。
2.  在文件夹的空白处，**按住键盘的Shift键**，同时点击鼠标右键。
3.  在弹出的菜单中，选择“**在此处打开命令窗口**”（Windows 10可能是“在此处打开PowerShell窗口”）。
4.  在弹出的命令窗口中，输入 `jupyter notebook` 并回车。
这样，打开的Jupyter Notebook页面就会直接定位到当前文件夹，方便你找到代码文件。

### Mac OS系统启动方法

在Mac OS系统上，可以通过以下方式启动：
1.  **通过终端运行**：打开“终端”（Terminal），输入 `jupyter notebook` 并回车。这是推荐的方式，速度较快。
2.  **通过Anaconda Navigator运行**：打开“Anaconda Navigator”应用，找到Jupyter Notebook图标并点击“Launch”。这种方式启动速度可能较慢。

Mac OS系统同样支持在指定路径打开终端并运行Jupyter Notebook，操作方法与Windows类似：在文件夹上右键，选择“New Terminal at Folder”之类的选项，然后在弹出的终端中输入命令即可。

![](img/5ab0c9d5deb367c483ed0fd6b4c160ee_15.png)

## 设置默认浏览器

![](img/5ab0c9d5deb367c483ed0fd6b4c160ee_17.png)

![](img/5ab0c9d5deb367c483ed0fd6b4c160ee_19.png)

![](img/5ab0c9d5deb367c483ed0fd6b4c160ee_21.png)

如果你按照上述步骤操作后，运行 `jupyter notebook` 无法自动弹出浏览器窗口，可能需要将Chrome浏览器设置为Jupyter Notebook的默认浏览器。请按以下步骤操作：

![](img/5ab0c9d5deb367c483ed0fd6b4c160ee_23.png)

![](img/5ab0c9d5deb367c483ed0fd6b4c160ee_25.png)

1.  **生成配置文件**：打开命令窗口（CMD或Anaconda Prompt），输入以下命令并回车：
    ```bash
    jupyter notebook --generate-config
    ```
    此命令会在用户目录下的 `.jupyter` 文件夹中生成一个配置文件 `jupyter_notebook_config.py`。

![](img/5ab0c9d5deb367c483ed0fd6b4c160ee_27.png)

![](img/5ab0c9d5deb367c483ed0fd6b4c160ee_29.png)

2.  **修改配置文件**：
    *   用记事本（或任何文本编辑器）打开上一步生成的配置文件。
    *   在文件中添加以下代码：
        ```python
        import webbrowser
        webbrowser.register('chrome', None, webbrowser.GenericBrowser('你的Chrome浏览器所在路径'))
        c.NotebookApp.browser = 'chrome'
        ```
    *   **关键步骤**：将代码中的 `你的Chrome浏览器所在路径` 替换为你电脑上Chrome浏览器的实际安装路径。
        *   如何查找路径：在开始菜单找到Google Chrome，右键选择“属性”，在“目标”一栏中显示的完整路径就是所需路径。
    *   保存并关闭配置文件。

完成设置后，再次运行 `jupyter notebook`，它就会自动使用Chrome浏览器打开了。

![](img/5ab0c9d5deb367c483ed0fd6b4c160ee_31.png)

## 课程总结

![](img/5ab0c9d5deb367c483ed0fd6b4c160ee_33.png)

本节课我们一起学习了量化金融Python编程的第一步——环境搭建。我们介绍了Anaconda软件的下载与安装，演示了如何在Windows和Mac OS系统上启动Jupyter Notebook，并解决了可能遇到的浏览器设置问题。现在，你的编程环境已经准备就绪，下一节课我们将开始学习Python编程基础。