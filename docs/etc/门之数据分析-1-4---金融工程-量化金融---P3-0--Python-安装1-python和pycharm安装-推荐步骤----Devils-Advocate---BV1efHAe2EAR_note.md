# 量化交易：Python入门之数据分析：1：Python与PyCharm环境搭建 🛠️

在本节课中，我们将学习如何为量化交易的数据分析搭建Python编程环境。主要内容包括安装Python解释器、安装集成开发环境PyCharm，以及配置国内镜像源以加速后续的包安装过程。

## 下载与安装PyCharm

上一节我们介绍了课程目标，本节中我们来看看如何获取并安装PyCharm。PyCharm是一个功能强大的Python集成开发环境。

![](img/7304b9d2fe703f730e9d0aae8ca67596_1.png)

首先，我们需要访问PyCharm的官方网站进行下载。

![](img/7304b9d2fe703f730e9d0aae8ca67596_3.png)

![](img/7304b9d2fe703f730e9d0aae8ca67596_1.png)

![](img/7304b9d2fe703f730e9d0aae8ca67596_5.png)

在官网页面找到下载区域。

![](img/7304b9d2fe703f730e9d0aae8ca67596_3.png)

点击下载按钮。

![](img/7304b9d2fe703f730e9d0aae8ca67596_5.png)

页面提供专业版下载。专业版软件需要激活码，可以在相关平台购买。如果没有激活码，也可以使用30天的免费试用期。

![](img/7304b9d2fe703f730e9d0aae8ca67596_7.png)

下载完成后，运行安装程序。安装过程中，可以自定义安装路径，例如从默认的C盘更改为D盘或E盘。建议创建桌面快捷方式并勾选“添加到PATH环境变量”选项。

![](img/7304b9d2fe703f730e9d0aae8ca67596_9.png)

![](img/7304b9d2fe703f730e9d0aae8ca67596_7.png)

完成安装后，重启计算机以使配置生效。

## 配置PyCharm与创建项目

上一节我们完成了PyCharm的安装，本节中我们来看看如何首次运行并创建一个Python项目。

重启后，打开PyCharm。首次启动时，需要选择激活方式。如果拥有激活码，选择“Activate PyCharm”；否则，可以选择“Start a free 30-day trial”开始试用。

进入主界面后，点击“New Project”来创建一个新项目。在项目创建界面，选择项目类型为“Python Project”。

![](img/7304b9d2fe703f730e9d0aae8ca67596_11.png)

设置项目存储位置，可以新建一个文件夹，例如 `python_project`。在“Python Interpreter”部分，选择Python版本，例如3.11.9。保持“Create a virtual environment using Venv”的默认选项。点击“Create”按钮创建项目。

![](img/7304b9d2fe703f730e9d0aae8ca67596_11.png)

此时，PyCharm会自动下载并配置所选版本的Python解释器。需要理解的是，Python是一个独立的编程语言解释器，而PyCharm、Jupyter Notebook、VS Code等是用于编写和运行Python代码的界面软件。这些软件都需要背后有Python解释器的支持。

![](img/7304b9d2fe703f730e9d0aae8ca67596_13.png)

等待Python解释器安装完成。安装过程中，Windows Defender可能会弹出提示，需要允许PyCharm通过防火墙。

## 验证环境与基本操作

上一节我们创建了项目，本节中我们来看看如何验证环境并执行基本操作。

安装完成后，可以在项目中新建一个Python文件进行测试。

![](img/7304b9d2fe703f730e9d0aae8ca67596_13.png)

![](img/7304b9d2fe703f730e9d0aae8ca67596_15.png)

![](img/7304b9d2fe703f730e9d0aae8ca67596_15.png)

新建一个文件，例如 `test.py`。在文件中输入简单的代码：

```python
print("Hello")
```

PyCharm界面主要包含几个区域：代码编辑区、Python控制台和系统终端。运行代码可以通过快捷键 `Alt+Shift+E` 执行当前行，结果会显示在Python控制台。也可以在Python控制台中直接输入命令进行交互。

在文件资源管理器中，可以看到项目结构。`venv` 文件夹是为此项目创建的虚拟环境，用于隔离包依赖，不建议手动修改。`.idea` 文件夹是PyCharm的配置目录。

![](img/7304b9d2fe703f730e9d0aae8ca67596_17.png)

## 配置国内镜像源与安装必要包

上一节我们验证了基础环境，本节中我们来看看如何优化包管理并安装数据分析所需的库。

直接从国外官方源下载Python包速度可能较慢，因此需要配置国内镜像源，例如清华大学的镜像。

以下是配置镜像源的步骤，在PyCharm的终端中执行：

1.  首先，打开PyCharm底部的“Terminal”标签页。
2.  输入以下命令设置全局镜像源：

```bash
pip config set global.index-url https://pypi.tuna.tsinghua.edu.cn/simple
```

此命令将包下载源指向清华大学镜像站，可以显著提升下载速度。

配置好镜像源后，即可安装后续课程需要的Python包。以下是需要安装的核心数据分析库：

*   **NumPy**：提供强大的多维数组对象和数学函数。
*   **Pandas**：提供高性能、易用的数据结构和数据分析工具。

在终端中依次执行以下安装命令：

```bash
pip install numpy
pip install pandas
```

如果安装速度依然不理想，可以使用 `-i` 参数指定镜像源进行安装：

```bash
pip install numpy -i https://pypi.tuna.tsinghua.edu.cn/simple
pip install pandas -i https://pypi.tuna.tsinghua.edu.cn/simple
```

安装完成后，可以在Python控制台中导入这些库以验证安装是否成功：

```python
import numpy as np
import pandas as pd
```

如果没有报错，说明环境配置成功。

![](img/7304b9d2fe703f730e9d0aae8ca67596_19.png)

![](img/7304b9d2fe703f730e9d0aae8ca67596_19.png)

本节课中我们一起学习了量化交易数据分析的Python环境搭建。我们完成了从下载安装PyCharm、创建配置Python项目，到设置国内镜像源并安装NumPy和Pandas核心库的全过程。现在，你的开发环境已经准备就绪，可以开始后续的Python数据分析学习了。