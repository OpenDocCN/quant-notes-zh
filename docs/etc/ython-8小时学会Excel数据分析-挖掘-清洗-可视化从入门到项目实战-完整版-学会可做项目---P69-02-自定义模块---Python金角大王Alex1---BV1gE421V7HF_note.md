# Python数据分析与可视化：P69：02 自定义模块 🧩

![](img/3cac016a50072e0a8b61594bf952e6b4_1.png)

在本节课中，我们将学习如何创建和使用自定义模块。之前我们导入的都是Python官方自带的模块，现在我们将自己编写一个模块，并了解如何在不同目录下成功导入它。

![](img/3cac016a50072e0a8b61594bf952e6b4_3.png)

## 创建自定义模块

![](img/3cac016a50072e0a8b61594bf952e6b4_5.png)

上一节我们介绍了如何导入标准库模块，本节中我们来看看如何创建自己的模块。创建一个自定义模块非常简单，只需创建一个Python文件（.py），并在其中定义函数或变量即可。

![](img/3cac016a50072e0a8b61594bf952e6b4_7.png)

我们创建一个名为 `my_first_module.py` 的文件，并写入以下代码：

![](img/3cac016a50072e0a8b61594bf952e6b4_9.png)

```python
def say_hi():
    print("我是被调用的")

![](img/3cac016a50072e0a8b61594bf952e6b4_11.png)

print("哈哈哈")
```

![](img/3cac016a50072e0a8b61594bf952e6b4_13.png)

为了验证一个观点：**当模块被导入时，该模块文件中的所有顶层代码都会被执行**。我们在函数外直接写了一句 `print("哈哈哈")`。如果从其他地方导入此模块时执行了这行打印，就证明了这个观点。

## 导入并测试自定义模块

接下来，我们在同一目录下创建另一个脚本文件来导入并测试我们的自定义模块。

```python
import my_first_module

my_first_module.say_hi()
```

![](img/3cac016a50072e0a8b61594bf952e6b4_15.png)

![](img/3cac016a50072e0a8b61594bf952e6b4_17.png)

执行这个脚本，你会看到控制台先输出“哈哈哈”，然后输出“我是被调用的”。这证实了导入模块时会执行模块文件中的所有顶层代码。调用模块内的函数则与调用标准库模块函数的方式完全相同。

## 跨目录导入的问题

![](img/3cac016a50072e0a8b61594bf952e6b4_19.png)

![](img/3cac016a50072e0a8b61594bf952e6b4_21.png)

现在，我们尝试从一个不同的目录导入 `my_first_module`。例如，在上级目录或另一个文件夹中创建一个新脚本 `module_import_test.py`，并尝试导入。

![](img/3cac016a50072e0a8b61594bf952e6b4_23.png)

![](img/3cac016a50072e0a8b61594bf952e6b4_25.png)

```python
import my_first_module
```

此时，你很可能会遇到 `ModuleNotFoundError: No module named 'my_first_module'` 错误。这是因为Python解释器不知道去哪里寻找这个模块文件。

![](img/3cac016a50072e0a8b61594bf952e6b4_27.png)

## Python的模块查找路径

![](img/3cac016a50072e0a8b61594bf952e6b4_29.png)

Python解释器根据一个名为 `sys.path` 的列表来查找模块。这个列表包含了所有搜索模块的目录路径。解释器会按顺序在这些路径中查找，找到即停止，如果所有路径中都找不到，则报错。

![](img/3cac016a50072e0a8b61594bf952e6b4_31.png)

![](img/3cac016a50072e0a8b61594bf952e6b4_33.png)

我们可以通过 `sys` 模块来查看这个路径列表：

![](img/3cac016a50072e0a8b61594bf952e6b4_35.png)

```python
import sys
print(sys.path)
```

![](img/3cac016a50072e0a8b61594bf952e6b4_37.png)

输出结果是一个路径列表。**列表的第一个元素通常是当前执行脚本所在的目录**。这就是为什么在同一目录下可以成功导入模块。当脚本位置改变，其所在目录不再是模块文件的目录时，解释器在 `sys.path` 列出的所有路径中都找不到我们的模块文件，因此报错。

![](img/3cac016a50072e0a8b61594bf952e6b4_39.png)

![](img/3cac016a50072e0a8b61594bf952e6b4_41.png)

## 解决方案：动态添加模块路径

![](img/3cac016a50072e0a8b61594bf952e6b4_43.png)

为了让我们的模块能在任何地方被导入，我们需要将其所在的目录添加到 `sys.path` 中。以下是具体步骤：

1.  **获取模块文件的绝对路径**：使用 `__file__` 变量可以获取当前脚本文件的绝对路径。
2.  **推导出模块目录的路径**：使用 `os.path` 模块中的函数来处理路径。
3.  **将路径添加到 `sys.path`**：使用 `sys.path.append()` 方法。

以下是实现代码：

```python
import sys
import os

![](img/3cac016a50072e0a8b61594bf952e6b4_45.png)

# 获取当前脚本的绝对路径
current_file_path = __file__
# 获取当前脚本的目录（去掉文件名）
current_dir = os.path.dirname(current_file_path)
# 获取上一级目录（即模块所在的目录）
parent_dir = os.path.dirname(current_dir)

# 将模块所在目录添加到 sys.path
sys.path.append(parent_dir)

# 现在可以成功导入了
import my_first_module
my_first_module.say_hi()
```

![](img/3cac016a50072e0a8b61594bf952e6b4_47.png)

![](img/3cac016a50072e0a8b61594bf952e6b4_49.png)

**代码解释**：
*   `os.path.dirname(__file__)`：去掉路径中的文件名，返回其所在目录。
*   连续使用两次 `os.path.dirname()`，是为了获取当前脚本的上层目录（即 `my_first_module.py` 所在的目录）。
*   `sys.path.append(parent_dir)`：将这个目录路径添加到模块搜索列表中。

这种方法的好处是**动态**的。无论你将项目文件夹复制到哪台电脑、哪个位置，这段代码都能自动计算出正确的路径并添加，无需手动修改。

![](img/3cac016a50072e0a8b61594bf952e6b4_51.png)

## 总结

![](img/3cac016a50072e0a8b61594bf952e6b4_53.png)

![](img/3cac016a50072e0a8b61594bf952e6b4_55.png)

本节课中我们一起学习了Python自定义模块的核心知识：
1.  **创建模块**：任何 `.py` 文件都是一个模块。
2.  **导入执行**：导入模块时，会执行该模块中的所有顶层代码。
3.  **查找机制**：Python通过 `sys.path` 列表中的路径来查找模块。
4.  **跨目录导入**：通过动态获取并添加模块所在目录到 `sys.path` 中，可以实现跨目录的模块导入。

![](img/3cac016a50072e0a8b61594bf952e6b4_57.png)

![](img/3cac016a50072e0a8b61594bf952e6b4_59.png)

理解模块的查找和导入机制，对于组织大型项目代码结构至关重要。