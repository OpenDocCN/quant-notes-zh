# Python包管理：1：pip与虚拟环境入门指南 🐍

![](img/9a7f9f9977f6a0a28a7fed510b61558b_1.png)

在本节课中，我们将要学习Python包管理的基础知识，特别是如何使用`pip`和虚拟环境（`venv`）来安装和管理第三方库。这是解决项目依赖冲突、确保代码在不同环境下正常运行的关键技能。

![](img/9a7f9f9977f6a0a28a7fed510b61558b_3.png)

上一节我们介绍了项目背景和遇到的问题，本节中我们来看看如何系统地管理Python包。

![](img/9a7f9f9977f6a0a28a7fed510b61558b_5.png)

![](img/9a7f9f9977f6a0a28a7fed510b61558b_7.png)

---

![](img/9a7f9f9977f6a0a28a7fed510b61558b_9.png)

![](img/9a7f9f9977f6a0a28a7fed510b61558b_11.png)

## 概述：为何需要包管理与虚拟环境？

![](img/9a7f9f9977f6a0a28a7fed510b61558b_13.png)

在Python开发中，我们经常需要使用他人编写的代码库，这些库被称为“包”或“模块”。`pip`是Python的包安装器，用于从Python包索引（PyPI）下载和安装这些包。

![](img/9a7f9f9977f6a0a28a7fed510b61558b_15.png)

然而，直接在系统全局Python环境中安装所有包会遇到问题：不同项目可能需要同一个包的不同版本，这会导致版本冲突。虚拟环境（`venv`）就是为了解决这个问题而生的工具，它能为每个项目创建一个独立的Python运行环境，互不干扰。

![](img/9a7f9f9977f6a0a28a7fed510b61558b_17.png)

![](img/9a7f9f9977f6a0a28a7fed510b61558b_18.png)

---

![](img/9a7f9f9977f6a0a28a7fed510b61558b_20.png)

![](img/9a7f9f9977f6a0a28a7fed510b61558b_22.png)

## 检查pip是否已安装

![](img/9a7f9f9977f6a0a28a7fed510b61558b_24.png)

大多数较新的Python版本都自带`pip`。首先，我们需要检查它是否已正确安装。

![](img/9a7f9f9977f6a0a28a7fed510b61558b_26.png)

在终端或命令行中执行以下命令：
```bash
pip --version
```
或者，如果你的系统同时安装了Python 2和Python 3，可能需要使用：
```bash
pip3 --version
```
如果命令成功执行并显示了版本号（例如 `pip 23.3.1 from ...`），说明`pip`已安装。如果出现“command not found”错误，则需先安装`pip`。

---

![](img/9a7f9f9977f6a0a28a7fed510b61558b_28.png)

![](img/9a7f9f9977f6a0a28a7fed510b61558b_30.png)

![](img/9a7f9f9977f6a0a28a7fed510b61558b_32.png)

## 使用pip安装包

![](img/9a7f9f9977f6a0a28a7fed510b61558b_34.png)

确认`pip`可用后，就可以用它来安装包了。基本安装命令格式如下：
```bash
pip install <package_name>
```
例如，安装名为 `requests` 的库：
```bash
pip install requests
```
有时，安装包需要系统权限。在Linux或macOS上，你可能会遇到权限错误。此时，可以尝试以下两种方法之一：
1.  使用 `sudo` 以管理员权限安装（不推荐用于全局安装，可能导致系统包管理混乱）：
    ```bash
    sudo pip install <package_name>
    ```
2.  **推荐**：使用 `--user` 标志将包安装到当前用户的目录下：
    ```bash
    pip install --user <package_name>
    ```

![](img/9a7f9f9977f6a0a28a7fed510b61558b_36.png)

![](img/9a7f9f9977f6a0a28a7fed510b61558b_38.png)

![](img/9a7f9f9977f6a0a28a7fed510b61558b_40.png)

---

![](img/9a7f9f9977f6a0a28a7fed510b61558b_42.png)

![](img/9a7f9f9977f6a0a28a7fed510b61558b_44.png)

## 为何以及如何使用虚拟环境（venv）

直接在全局环境安装所有包，就像把所有工具都堆在客厅。当不同项目需要不同版本的工具时，就会变得混乱不堪。虚拟环境为每个项目提供了一个独立的“工具箱”（即独立的Python环境）。

以下是创建和使用虚拟环境的步骤：

![](img/9a7f9f9977f6a0a28a7fed510b61558b_46.png)

1.  **创建虚拟环境**：
    进入你的项目目录，然后运行以下命令。这会在当前目录下创建一个名为 `venv`（可自定义）的文件夹，里面包含独立的Python解释器和包管理目录。
    ```bash
    python3 -m venv venv
    ```

![](img/9a7f9f9977f6a0a28a7fed510b61558b_48.png)

2.  **激活虚拟环境**：
    -   **在macOS/Linux上**：
        ```bash
        source venv/bin/activate
        ```
    -   **在Windows上**：
        ```bash
        .\venv\Scripts\activate
        ```
    激活后，你的命令行提示符前通常会显示环境名（如 `(venv)`），表示你已进入该虚拟环境。

![](img/9a7f9f9977f6a0a28a7fed510b61558b_50.png)

3.  **在虚拟环境中安装包**：
    激活环境后，使用`pip install`安装的任何包都将只存在于这个虚拟环境中，不会影响全局环境或其他项目。
    ```bash
    (venv) pip install requests pandas numpy
    ```

4.  **停用虚拟环境**：
    工作完成后，只需执行以下命令即可退出当前虚拟环境，回到系统全局环境。
    ```bash
    deactivate
    ```

![](img/9a7f9f9977f6a0a28a7fed510b61558b_52.png)

---

![](img/9a7f9f9977f6a0a28a7fed510b61558b_54.png)

## 常见问题与解决思路

在实践中，你可能会遇到以下问题及其解决思路：

![](img/9a7f9f9977f6a0a28a7fed510b61558b_56.png)

![](img/9a7f9f9977f6a0a28a7fed510b61558b_58.png)

1.  **`pip` 命令未找到**：
    *   确保Python已正确安装并已添加到系统环境变量（PATH）中。
    *   尝试使用 `python -m pip` 来代替 `pip` 命令。

![](img/9a7f9f9977f6a0a28a7fed510b61558b_60.png)

2.  **安装包时权限被拒绝**：
    *   避免频繁使用 `sudo pip install`。
    *   优先使用 `pip install --user` 或创建虚拟环境进行安装。

3.  **已安装包但代码中无法导入（ImportError）**：
    *   最常见的原因是包没有安装在当前Python解释器对应的环境中。
    *   **检查**：在终端运行 `python`，然后尝试 `import package_name`，看是否报错。
    *   **解决**：确保你安装包的Python环境（全局或虚拟环境）与你运行代码的Python环境是同一个。使用虚拟环境可以完美规避此问题。

4.  **多个Python版本导致混淆**：
    *   使用 `python3` 和 `pip3` 来明确指定使用Python 3。
    *   在虚拟环境中，`python` 和 `pip` 命令会自动指向该环境内的版本。

---

## 总结

![](img/9a7f9f9977f6a0a28a7fed510b61558b_62.png)

![](img/9a7f9f9977f6a0a28a7fed510b61558b_64.png)

本节课中我们一起学习了Python包管理的基础。我们了解到`pip`是安装Python包的核心工具，而`venv`创建的虚拟环境是管理项目依赖、隔离不同项目环境的**最佳实践**。

核心要点回顾：
*   **检查pip**：使用 `pip --version`。
*   **安装包**：使用 `pip install <包名>`，推荐加 `--user` 标志或使用虚拟环境。
*   **虚拟环境**：
    *   创建：`python3 -m venv 环境名`
    *   激活：`source 环境名/bin/activate` (macOS/Linux)
    *   在激活的环境中安装包。
    *   停用：`deactivate`

![](img/9a7f9f9977f6a0a28a7fed510b61558b_66.png)

掌握这些技能，你就能为每个Python项目搭建干净、独立的开发环境，从而避免包版本冲突，让项目管理和协作变得更加顺畅。下一节，我们将回到最初的项目，应用这些知识来解决代码的导入问题。