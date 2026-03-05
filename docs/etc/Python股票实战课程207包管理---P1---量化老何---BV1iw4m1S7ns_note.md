# Python股票实战课程：207：包管理 📦

![](img/c58725a11b9f94b3241d2e9ae828d4eb_1.png)

在本节课中，我们将要学习Python中包（Package）的管理。包是Python强大扩展能力的体现，它允许我们安装和使用他人编写的功能模块，例如用于数据分析的`pandas`或用于获取股票数据的`tushare`。掌握包的安装、管理和调用，是构建复杂量化策略的基础。

## 什么是包？🤔

上一节我们介绍了Python环境和基础语法，本节中我们来看看如何扩展Python的功能。

![](img/c58725a11b9f94b3241d2e9ae828d4eb_3.png)

包，在Python环境中，指的是包含特定功能的模块集合。例如，在PyCharm的“Project Interpreter”设置中，我们可以看到已安装的包列表，如`gm`（掘金量化）或`pandas`。这些包提供了现成的函数和数据，让我们无需从头编写所有代码。

![](img/c58725a11b9f94b3241d2e9ae828d4eb_5.png)

![](img/c58725a11b9f94b3241d2e9ae828d4eb_7.png)

包的安装位置通常在Python环境的`site-packages`目录下。例如，在Anaconda环境中，路径可能类似于：`anaconda3/envs/py37/Lib/site-packages/`。所有通过`pip`或`conda`安装的第三方包都存储在这里。

![](img/c58725a11b9f94b3241d2e9ae828d4eb_9.png)

![](img/c58725a11b9f94b3241d2e9ae828d4eb_11.png)

## 如何安装包？🔧

![](img/c58725a11b9f94b3241d2e9ae828d4eb_13.png)

了解了包的定义后，接下来我们学习安装包的几种方法。

![](img/c58725a11b9f94b3241d2e9ae828d4eb_15.png)

安装包主要有两种方式：通过图形界面（如PyCharm）或通过命令行。

![](img/c58725a11b9f94b3241d2e9ae828d4eb_17.png)

![](img/c58725a11b9f94b3241d2e9ae828d4eb_19.png)

以下是图形界面安装步骤：
1.  在PyCharm中，打开 `File -> Settings -> Project: [项目名] -> Python Interpreter`。
2.  点击窗口右上角的 `+` 号。
3.  在搜索框中输入包名（如 `pandas`），从列表中选择并点击 `Install Package`。

![](img/c58725a11b9f94b3241d2e9ae828d4eb_21.png)

以下是命令行安装步骤（以`tushare`包为例）：
1.  打开命令行终端（Windows下可以是Anaconda Prompt或CMD）。
2.  激活你的Python环境（例如：`activate py37`）。
3.  使用 `pip` 命令安装：`pip install tushare`

![](img/c58725a11b9f94b3241d2e9ae828d4eb_23.png)

**核心安装命令公式**：
```bash
pip install [package_name]
```
或使用conda（如果包在conda仓库中）：
```bash
conda install [package_name]
```

![](img/c58725a11b9f94b3241d2e9ae828d4eb_25.png)

通常，`pip`和`conda`安装其中一个即可，无需重复安装。

![](img/c58725a11b9f94b3241d2e9ae828d4eb_27.png)

![](img/c58725a11b9f94b3241d2e9ae828d4eb_29.png)

## 如何导入和使用包？📥

成功安装包之后，我们需要在代码中导入它才能使用。

![](img/c58725a11b9f94b3241d2e9ae828d4eb_31.png)

在Python中，导入包有以下几种常用方式，每种方式在调用函数时略有不同。

![](img/c58725a11b9f94b3241d2e9ae828d4eb_33.png)

以下是三种主要的导入方式：
1.  **直接导入整个模块**：
    ```python
    import tushare
    # 调用时需要加上模块名前缀
    data = tushare.get_k_data('000001')
    ```
2.  **从模块中导入特定函数/子模块**：
    ```python
    from tushare import get_k_data
    # 调用时可以直接使用函数名
    data = get_k_data('000001')
    ```
    或者导入模块并起别名（常用于`pandas`和`numpy`）：
    ```python
    import tushare as ts
    data = ts.get_k_data('000001')
    ```
3.  **导入模块中的所有内容（不推荐，易引起命名冲突）**：
    ```python
    from tushare import *
    ```

![](img/c58725a11b9f94b3241d2e9ae828d4eb_35.png)

![](img/c58725a11b9f94b3241d2e9ae828d4eb_37.png)

**核心概念**：`import` 语句用于将外部模块的功能引入当前脚本。使用 `import module` 方式调用时需要 `module.function()`；使用 `from module import function` 方式则可以直接 `function()`。

![](img/c58725a11b9f94b3241d2e9ae828d4eb_39.png)

## 创建和导入自己的模块 🛠️

![](img/c58725a11b9f94b3241d2e9ae828d4eb_41.png)

除了使用第三方包，我们也可以将自己编写的`.py`文件作为模块导入，这对于代码复用和组织至关重要。

上一节我们介绍了如何导入第三方包，本节中我们来看看如何导入自己编写的代码文件。

![](img/c58725a11b9f94b3241d2e9ae828d4eb_43.png)

假设我们有一个自己编写的函数文件 `func.py`，它与主程序 `main.py` 在同一个目录下。

![](img/c58725a11b9f94b3241d2e9ae828d4eb_45.png)

**`func.py` 内容示例**：
```python
def add_two_numbers(a, b):
    """一个简单的加法函数"""
    return a + b
```

在 `main.py` 中，我们可以这样导入并使用它：
```python
# 从 func 模块中导入 add_two_numbers 函数
from func import add_two_numbers

# 调用函数
result = add_two_numbers(3, 5)
print(f"3 + 5 = {result}")  # 输出：3 + 5 = 8
```

![](img/c58725a11b9f94b3241d2e9ae828d4eb_47.png)

**关键点**：要成功导入自定义模块，该模块文件（如`func.py`）需要与当前脚本在**同一目录**下，或者位于Python解释器能够搜索到的路径（如`site-packages`）中。这种跨文件调用的能力意义重大，它允许我们：
1.  **构建个人代码库**：将常用函数集中管理，在不同策略中重复调用，避免代码冗余。
2.  **保护知识产权**：未来可以对`func.py`这类核心文件进行加密（编译成`.pyc`或`.pyd`文件），使他人无法直接查看源码，但依然可以调用其功能。

![](img/c58725a11b9f94b3241d2e9ae828d4eb_49.png)

## 本节总结 📝

![](img/c58725a11b9f94b3241d2e9ae828d4eb_51.png)

本节课中我们一起学习了Python包管理的核心知识：
1.  **包的概念**：包是扩展Python功能的第三方模块集合。
2.  **包的安装**：掌握了通过PyCharm图形界面和使用`pip install`命令行两种安装方法。
3.  **包的导入与使用**：学习了`import module`和`from module import function`等导入方式及其调用区别。
4.  **自定义模块**：学会了如何将自己编写的`.py`文件作为模块导入和使用，这是实现代码复用和模块化编程的基础。

![](img/c58725a11b9f94b3241d2e9ae828d4eb_53.png)

掌握包的管理和模块化编程思想，将极大地提升你开发量化策略的效率和代码的可维护性。