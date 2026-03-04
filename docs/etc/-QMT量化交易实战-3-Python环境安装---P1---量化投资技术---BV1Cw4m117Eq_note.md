# QMT量化交易实战：3：Python环境安装 🐍

在本节课中，我们将学习如何为QMT量化交易软件搭建Python开发环境。由于QMT基于Python语言，因此我们需要在本地安装Python环境、代码编辑器以及相关的开发工具。课程将介绍两种安装方式，并指导你配置高效的开发工作流。

## 概述

本节内容涵盖Python环境安装的两种主要方式、代码编辑器的选择、Git版本控制工具的安装、国内镜像源的配置以及Python包管理器的使用。我们将从基础安装开始，逐步构建一个灵活且高效的本地开发环境。

![](img/836af073ab7661a9068b095039e414f4_1.png)

## Python环境安装方式

![](img/836af073ab7661a9068b095039e414f4_3.png)

![](img/836af073ab7661a9068b095039e414f4_5.png)

Python环境有两种主流安装方式：通过官网直接安装和使用`pyenv`进行多版本管理。

![](img/836af073ab7661a9068b095039e414f4_7.png)

### 方式一：官网安装

第一种方式是通过Python官网下载安装包进行安装。这种方式操作简单，适合初学者。

*   **官网地址**：`https://www.python.org/downloads/`
*   **版本建议**：推荐下载Python 3.8或3.10等稳定版本。你可以在官网找到历史版本进行下载。
*   **安装流程**：下载对应操作系统的安装包（如Windows的`.exe`文件），按照向导完成安装即可。

![](img/836af073ab7661a9068b095039e414f4_9.png)

### 方式二：使用pyenv进行多版本管理

![](img/836af073ab7661a9068b095039e414f4_11.png)

![](img/836af073ab7661a9068b095039e414f4_12.png)

![](img/836af073ab7661a9068b095039e414f4_14.png)

第二种方式是使用`pyenv`工具。它允许你在同一台电脑上安装和管理多个Python版本，非常适合需要同时维护多个不同Python版本项目的开发者。

![](img/836af073ab7661a9068b095039e414f4_16.png)

*   **工具介绍**：`pyenv`是一个流行的Python版本管理工具，在GitHub上拥有很高的关注度。
*   **安装命令（Windows）**：在PowerShell中执行以下命令进行安装。
    ```powershell
    Invoke-WebRequest -UseBasicParsing -Uri "https://raw.githubusercontent.com/pyenv-win/pyenv-win/master/pyenv-win/install-pyenv-win.ps1" -OutFile "./install-pyenv-win.ps1"; &"./install-pyenv-win.ps1"
    ```
*   **基本使用**：
    *   查看可安装版本：`pyenv install -l`
    *   安装特定版本（如3.10.11）：`pyenv install 3.10.11`
    *   查看已安装版本：`pyenv versions`
    *   设置全局Python版本：`pyenv global 3.10.11`
    *   设置局部目录Python版本：`pyenv local 3.8.10`

![](img/836af073ab7661a9068b095039e414f4_18.png)

**安装提示**：使用`pyenv`安装时可能因网络问题速度较慢。如果遇到困难，建议初学者优先采用第一种官网安装方式。

## 代码编辑器与Git工具

![](img/836af073ab7661a9068b095039e414f4_20.png)

![](img/836af073ab7661a9068b095039e414f4_22.png)

一个顺手的代码编辑器和版本控制工具对开发至关重要。

![](img/836af073ab7661a9068b095039e414f4_24.png)

![](img/836af073ab7661a9068b095039e414f4_26.png)

### 代码编辑器：VS Code

我们推荐使用Visual Studio Code（VS Code）作为代码编辑器。它是一款轻量级但功能强大的跨平台编辑器，拥有丰富的Python插件生态。

![](img/836af073ab7661a9068b095039e414f4_28.png)

*   **下载地址**：`https://code.visualstudio.com/`
*   **替代选择**：你也可以选择专业的Python IDE，如PyCharm。

![](img/836af073ab7661a9068b095039e414f4_30.png)

### 版本控制工具：Git

Git是分布式版本控制系统，用于管理代码的变更历史。在Windows环境下，我们通常安装Git Bash（Git for Windows），它集成了Git和一個好用的终端。

![](img/836af073ab7661a9068b095039e414f4_32.png)

*   **下载地址**：`https://git-scm.com/downloads`
*   **作用**：不仅用于代码管理，也便于从GitHub等平台拉取代码。

## 配置Python国内镜像源

![](img/836af073ab7661a9068b095039e414f4_34.png)

默认情况下，Python的包管理器`pip`从国外官方源下载包，速度可能很慢。配置国内镜像源可以显著提升下载速度。

![](img/836af073ab7661a9068b095039e414f4_36.png)

以下是配置`pip`使用清华大学镜像源的方法：

1.  在用户目录下（如`C:\Users\你的用户名\`）创建`pip`文件夹。
2.  在`pip`文件夹内创建`pip.ini`文件。
3.  在`pip.ini`文件中输入以下内容：
    ```ini
    [global]
    index-url = https://pypi.tuna.tsinghua.edu.cn/simple
    trusted-host = pypi.tuna.tsinghua.edu.cn
    ```

![](img/836af073ab7661a9068b095039e414f4_38.png)

**常用镜像源列表**：
*   清华大学：`https://pypi.tuna.tsinghua.edu.cn/simple`
*   阿里云：`https://mirrors.aliyun.com/pypi/simple/`
*   腾讯云：`https://mirrors.cloud.tencent.com/pypi/simple`

**注意**：部分镜像源（如阿里云）可能屏蔽某些被认定为有风险的第三方包（如一些爬虫框架）。若遇到包安装失败，可尝试切换镜像源。

![](img/836af073ab7661a9068b095039e414f4_40.png)

## 使用Poetry管理项目依赖

![](img/836af073ab7661a9068b095039e414f4_42.png)

![](img/836af073ab7661a9068b095039e414f4_44.png)

Python默认的`pip`工具是全局包管理器。当本地有多个项目，且各自依赖不同版本的包时，使用`pip`管理会变得复杂。`Poetry`是一个优秀的Python项目依赖管理和打包工具。

### 安装Poetry

![](img/836af073ab7661a9068b095039e414f4_46.png)

在终端中执行以下命令进行全局安装：
```bash
pip install poetry
```

![](img/836af073ab7661a9068b095039e414f4_48.png)

### 使用Poetry创建并管理项目

以下是使用Poetry的基本工作流程：

1.  **初始化新项目**：在目标目录下执行 `poetry init`，根据提示创建 `pyproject.toml` 配置文件。
2.  **添加依赖包**：例如，为项目添加 `pandas` 库，执行 `poetry add pandas`。Poetry会自动创建虚拟环境并安装依赖。
3.  **运行脚本**：在项目目录下，使用 `poetry run python your_script.py` 来运行Python脚本，以确保使用项目自身的虚拟环境。

![](img/836af073ab7661a9068b095039e414f4_50.png)

**优势**：Poetry为每个项目创建独立的虚拟环境，精确管理依赖包及其版本，避免了项目间的依赖冲突。

![](img/836af073ab7661a9068b095039e414f4_52.png)

## 环境验证与测试

![](img/836af073ab7661a9068b095039e414f4_54.png)

完成上述安装和配置后，我们可以进行简单的测试来验证环境是否正常工作。

![](img/836af073ab7661a9068b095039e414f4_56.png)

1.  **验证Python**：在终端输入 `python --version`，检查显示的版本是否正确。
2.  **验证包安装与镜像源**：尝试安装一个包，如 `pip install backtrader`，观察下载速度是否来自配置的国内镜像源。
3.  **验证Poetry项目**：在VS Code中打开用Poetry创建的项目，编写一个简单的测试脚本（例如使用pandas打印一个DataFrame），并通过 `poetry run` 命令执行，确认能正常运行。

## 总结

![](img/836af073ab7661a9068b095039e414f4_58.png)

本节课我们一起学习了搭建QMT量化交易Python开发环境的完整流程。我们介绍了两种安装Python的方式，推荐了VS Code编辑器和Git工具，讲解了配置国内镜像源以加速下载的方法，并引入了Poetry这一强大的项目依赖管理工具。

![](img/836af073ab7661a9068b095039e414f4_60.png)

对于初学者，建议从官网安装Python和直接使用`pip`开始，先让程序运行起来。随着项目复杂度增加，可以逐步采用`pyenv`和`Poetry`来构建更灵活、更专业的开发环境。现在，你的本地Python开发环境已经准备就绪，可以开始后续的量化策略开发了。