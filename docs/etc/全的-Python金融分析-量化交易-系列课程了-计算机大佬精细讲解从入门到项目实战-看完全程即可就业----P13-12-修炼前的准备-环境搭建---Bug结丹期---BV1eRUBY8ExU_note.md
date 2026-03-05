# Python金融分析+量化交易：P13：12 修炼前的准备-环境搭建 🛠️

## 概述
在本节课中，我们将学习如何搭建数据分析与量化交易的开发环境。我们将重点介绍两个核心工具：Anaconda集成环境和Jupyter Notebook可视化开发工具，并详细讲解其安装与基本使用方法。

---

## Anaconda：数据分析的集成环境

上一节我们对数据分析进行了初步介绍。本节中，我们来看看数据分析所对应的开发环境搭建流程。

首先，我们需要了解一个名为 **Anaconda** 的工具。它是一个集成环境，已经为我们打包好了数据分析和机器学习开发所需的大部分库和工具。这意味着，安装好Anaconda后，我们就拥有了一个可以立即开始数据分析工作的环境。

以下是关于Anaconda的关键点：
*   **定义**：Anaconda是一个**集成环境**，它集成了数据分析和机器学习开发所需的全部环境。
*   **安装**：你需要从Anaconda官网下载对应你操作系统（Windows、macOS或Linux）的安装包。
*   **安装注意事项**：安装目录**不可以包含中文或特殊符号**，建议安装在某个磁盘的根目录下（例如 `C:\` 或 `D:\`）。安装过程通常是“下一步”的傻瓜式操作。

> 详细的图文安装步骤，我会提供一个Word文档供大家参考。

![](img/ab0be6872ebabd1b77cb2afce4ab08ea_1.png)

![](img/ab0be6872ebabd1b77cb2afce4ab08ea_3.png)

---

## Jupyter Notebook：基于浏览器的开发工具

安装好Anaconda后，我们就拥有了开发环境。接下来，我们需要一个可视化的工具来编写和运行代码。这个工具就是 **Jupyter Notebook**。

![](img/ab0be6872ebabd1b77cb2afce4ab08ea_5.png)

![](img/ab0be6872ebabd1b77cb2afce4ab08ea_7.png)

Jupyter Notebook是Anaconda提供的一个基于浏览器的可视化开发工具。我们不需要单独安装它，Anaconda安装完成后即可使用。我们将在Jupyter Notebook中编写和执行数据分析相关的代码。

### 启动Jupyter Notebook
以下是启动Jupyter Notebook的步骤：
1.  打开你的终端（或命令提示符/Anaconda Prompt）。
2.  输入指令 `jupyter notebook` 并按下回车。
3.  指令执行后，会自动启动你的默认浏览器，并显示一个文件目录界面。

### 创建与使用Notebook文件
启动Jupyter后，界面会显示当前目录的文件结构。我们需要新建一个Python源文件来编写代码。

以下是操作步骤：
1.  点击页面右上角的 **“New”** 按钮。
2.  在下拉菜单中选择 **“Python 3”**。这将创建一个新的Notebook源文件（后缀为 `.ipynb`）。
3.  创建后，你可以重命名该文件（例如改为 `my_analysis.ipynb`）。

### 理解Cell（单元格）
Notebook文件由一个个 **Cell（单元格）** 组成，这是编写内容的基本单元。

Cell有两种主要模式：
*   **Code模式**：用于**编写和运行程序代码**。
*   **Markdown模式**：用于**编写格式化的笔记和注释**。

你可以在Cell上方的下拉菜单中切换这两种模式。**无论哪种模式，编写完内容后都需要运行（Run）才能看到效果**。

### 常用快捷键
熟练使用快捷键可以极大提升在Jupyter中的工作效率。以下是核心快捷键列表：

*   **添加Cell**：
    *   `A`：在当前选中的Cell**上方**插入一个新Cell。
    *   `B`：在当前选中的Cell**下方**插入一个新Cell。
*   **删除Cell**：`X` 删除当前选中的Cell。
*   **切换Cell模式**：
    *   `M`：将当前Cell切换到 **Markdown模式**。
    *   `Y`：将当前Cell切换到 **Code模式**。
*   **运行Cell**：`Shift + Enter` 运行当前Cell，并跳转到下一个Cell。
*   **代码自动补全**：`Tab` 键可以触发代码自动补全提示。
*   **查看帮助文档**：将光标放在某个函数（如 `print`）上，按 `Shift + Tab` 可以弹出该函数的帮助文档。

---

## 总结
本节课中，我们一起学习了数据分析环境的搭建。
1.  我们首先介绍了 **Anaconda**，它是一个集成了数据科学所需各种库和工具的软件包，只需安装它即可获得完整的开发环境。
2.  接着，我们学习了Anaconda自带的 **Jupyter Notebook** 工具，它是一个基于浏览器的交互式编程环境。我们掌握了如何启动Jupyter、创建Notebook文件、使用两种模式的Cell（Code和Markdown）以及一系列提高效率的快捷键。

![](img/ab0be6872ebabd1b77cb2afce4ab08ea_9.png)

环境搭建是进行后续所有数据分析与量化交易实战的第一步。请务必根据指导完成Anaconda的安装，并熟悉Jupyter Notebook的基本操作。准备好后，我们就可以正式进入代码实战环节了。