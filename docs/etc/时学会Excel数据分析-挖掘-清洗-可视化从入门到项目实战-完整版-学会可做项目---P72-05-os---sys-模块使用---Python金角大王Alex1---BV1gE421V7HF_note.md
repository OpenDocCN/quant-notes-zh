# Python数据分析实战：P72：05：os与sys模块使用指南 🐍

![](img/132b110a370854b39391f3cf482ac90d_1.png)

在本节课中，我们将学习Python中两个非常实用的内置模块：`os`和`sys`。它们主要用于与操作系统交互和获取系统信息，是文件操作和脚本控制的基础工具。我们将重点介绍其中最常用的几个功能，并通过简单的例子帮助大家理解。

![](img/132b110a370854b39391f3cf482ac90d_3.png)

## 概述

![](img/132b110a370854b39391f3cf482ac90d_5.png)

`os`模块提供了丰富的函数来与操作系统进行交互，例如操作文件、目录和路径。`sys`模块则提供了与Python解释器及其环境交互的变量和函数。掌握它们能让你更灵活地控制Python程序。

---

## os模块常用功能

![](img/132b110a370854b39391f3cf482ac90d_7.png)

上一节我们概述了两个模块的作用，本节中我们来看看`os`模块的具体功能。以下是`os`模块中最常用的一些函数及其用途。

![](img/132b110a370854b39391f3cf482ac90d_9.png)

### 获取当前工作目录

`os.getcwd()`函数用于获取当前Python脚本的工作目录路径。这个路径是你从哪个目录启动Python脚本或交互式环境的路径。

![](img/132b110a370854b39391f3cf482ac90d_11.png)

![](img/132b110a370854b39391f3cf482ac90d_13.png)

```python
import os
current_path = os.getcwd()
print(current_path)
```

![](img/132b110a370854b39391f3cf482ac90d_15.png)

### 列出目录内容

![](img/132b110a370854b39391f3cf482ac90d_17.png)

`os.listdir()`函数可以获取指定目录下的所有文件和子目录的名称，并以列表形式返回。

```python
file_list = os.listdir('.')  # '.' 代表当前目录
for file in file_list:
    print(file)
```

![](img/132b110a370854b39391f3cf482ac90d_19.png)

### 文件与目录操作

![](img/132b110a370854b39391f3cf482ac90d_21.png)

以下是进行文件删除、目录删除和重命名的相关函数。

*   **删除文件**：`os.remove('filename')`
*   **删除目录**：`os.rmdir('dirname')` (目录需为空)
*   **重命名文件**：`os.rename('old_name', 'new_name')`

### 路径检测与信息获取

这些函数帮助你检查和获取文件路径的详细信息。

*   **判断是否为文件**：`os.path.isfile('path')`
*   **判断是否为目录**：`os.path.isdir('path')`
*   **检查路径是否存在**：`os.path.exists('path')`
*   **获取绝对路径**：`os.path.abspath('filename')`
*   **获取纯文件名**：`os.path.basename('/path/to/filename')` -> 返回 `'filename'`
*   **获取目录名**：`os.path.dirname('/path/to/filename')` -> 返回 `'/path/to'`

### 执行系统命令与获取文件属性

`os.system()`允许你在Python中直接运行操作系统的shell命令。`os.stat()`可以获取文件的详细属性信息。

```python
# 运行系统命令（例如查看系统信息）
os.system('uname -a')

# 获取文件属性
file_stats = os.stat('some_file.py')
print(f"文件大小: {file_stats.st_size} 字节")  # 文件大小
print(f"最后修改时间: {file_stats.st_mtime}") # 最后修改时间戳
```

---

## sys模块核心功能

了解了`os`模块对文件系统的操作后，我们来看看`sys`模块。它主要管理Python运行时环境。对于初学者，目前只需掌握以下两个核心功能。

### 获取命令行参数

`sys.argv`是一个列表，包含了从命令行传递给Python脚本的所有参数。其中`sys.argv[0]`是脚本本身的名称。

假设有一个脚本`test.py`，内容如下：
```python
import sys
print(f"脚本名: {sys.argv[0]}")
print(f"第一个参数: {sys.argv[1]}")
```
在命令行中运行 `python test.py hello`，输出将是：
```
脚本名: test.py
第一个参数: hello
```

### 访问系统路径

`sys.path`是一个列表，指定了Python解释器搜索模块的路径。当你使用`import`语句时，解释器会在这个列表包含的目录中查找对应的模块。

```python
import sys
print(sys.path)
```

---

## 总结

本节课中我们一起学习了`os`和`sys`模块的基础用法。
*   `os`模块让我们能够轻松地操作文件系统，包括获取路径、管理文件/目录、执行系统命令等。
*   `sys`模块则提供了与Python解释器交互的接口，特别是获取脚本参数(`sys.argv`)和模块搜索路径(`sys.path`)。

![](img/132b110a370854b39391f3cf482ac90d_23.png)

对于初学者，无需死记硬背所有函数。建议在需要时查阅文档，并通过实际编写代码来熟悉这些常用功能。它们是你未来进行文件处理、数据清洗和构建自动化脚本的得力助手。