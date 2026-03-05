# Python数据分析与自动化：P68：01 模块介绍和调用方式 📦

![](img/8db38cfadb3a388c90327dfcd7fdf2e2_1.png)

在本节课中，我们将要学习Python模块化编程的核心概念。模块是组织代码、提高开发效率和代码复用性的重要工具。理解模块和包，并掌握其调用方式，是迈向高效Python编程的关键一步。

![](img/8db38cfadb3a388c90327dfcd7fdf2e2_3.png)

![](img/8db38cfadb3a388c90327dfcd7fdf2e2_5.png)

## 什么是模块和包？

上一节我们介绍了模块化编程的重要性，本节中我们来看看模块和包的具体定义。

一个Python文件（以`.py`为后缀）就可以被称为一个**模块**。模块是代码组织的基本单位，它将相关的函数、变量和类封装在一起。

当多个模块被组织在一个目录中，并且该目录包含一个名为`__init__.py`的文件时，这个目录就称为一个**包**。包是用于组织和管理模块的更高层级结构。

使用模块和包的主要好处有以下两点：
1.  **提高代码可维护性**：将大型项目分解为多个小模块，使代码结构清晰，便于团队协作和维护。
2.  **避免命名冲突**：在不同模块中定义的相同名称的变量或函数不会互相影响。

## Python模块的分类

了解了模块的基本概念后，我们来看看Python中模块的几种类型。

Python模块主要分为三类：
*   **内置标准模块（标准库）**：随Python解释器一同安装，无需额外下载。例如 `os`、`random`、`sys` 等。
*   **第三方开源模块**：由Python社区开发，需要通过包管理工具（如`pip`）从网络下载安装。例如 `pandas`、`numpy`、`requests` 等。
*   **自定义模块**：用户自己编写的Python文件，可以在自己的项目中导入使用。

![](img/8db38cfadb3a388c90327dfcd7fdf2e2_7.png)

![](img/8db38cfadb3a388c90327dfcd7fdf2e2_9.png)

## 模块的调用方式

现在，我们来学习如何调用这些模块。Python提供了多种导入模块的方式。

以下是几种常见的模块导入语法：

![](img/8db38cfadb3a388c90327dfcd7fdf2e2_11.png)

![](img/8db38cfadb3a388c90327dfcd7fdf2e2_13.png)

**1. 导入整个模块**
这是最直接的方式。导入后，需要使用 `模块名.功能名` 的格式来调用。
```python
import os
os.mkdir(‘test_folder‘)  # 使用 os 模块下的 mkdir 函数创建目录
```

![](img/8db38cfadb3a388c90327dfcd7fdf2e2_15.png)

**2. 从模块中导入特定功能**
如果你只需要使用模块中的某个特定函数或变量，可以使用这种方式。导入后可以直接使用该功能名。
```python
from os import mkdir, system
mkdir(‘another_folder‘)  # 直接使用 mkdir
system(‘ls‘)             # 直接使用 system 执行系统命令
```

![](img/8db38cfadb3a388c90327dfcd7fdf2e2_17.png)

**3. 导入模块并为其设置别名**
当模块名较长或容易冲突时，可以使用 `as` 关键字为其设置一个简短的别名。
```python
import numpy as np
from os.path import join as path_join
```

**4. 导入模块中的所有功能（不推荐）**
使用星号 `*` 可以导入模块中的所有公开功能。**但这种方式不推荐使用**，因为它会污染当前的命名空间，可能导致意外的名称覆盖，引发难以调试的错误。
```python
from os import *  # 不推荐
```

**重要提示**：一旦使用 `import` 语句导入一个模块，Python解释器就会执行该模块文件中的所有顶层代码。

## 总结

![](img/8db38cfadb3a388c90327dfcd7fdf2e2_19.png)

![](img/8db38cfadb3a388c90327dfcd7fdf2e2_21.png)

![](img/8db38cfadb3a388c90327dfcd7fdf2e2_23.png)

本节课中我们一起学习了Python模块化编程的基础知识。我们明确了**模块**是一个Python文件，**包**是包含`__init__.py`文件的目录。我们了解了模块的三种分类：内置模块、第三方模块和自定义模块。最后，我们重点掌握了四种模块调用方式：`import module`、`from module import func`、`import module as alias` 以及不推荐的 `from module import *`。理解并熟练运用这些知识，将帮助你构建结构清晰、易于维护的Python项目。