# 数据分析+金融量化+数据清洗：P4：03 numpy数组三种神秘创建方式 📊

![](img/2ec0c8051b77f5037cc4e18746533f6d_1.png)

![](img/2ec0c8051b77f5037cc4e18746533f6d_3.png)

在本节课中，我们将要学习数据分析“三剑客”之一——NumPy模块的基本使用，并重点掌握创建NumPy数组的三种核心方式。NumPy是进行数值计算和科学运算的基础，理解如何创建数组是后续所有操作的第一步。

![](img/2ec0c8051b77f5037cc4e18746533f6d_5.png)

![](img/2ec0c8051b77f5037cc4e18746533f6d_7.png)

## 概述：数据分析“三剑客”简介

![](img/2ec0c8051b77f5037cc4e18746533f6d_9.png)

![](img/2ec0c8051b77f5037cc4e18746533f6d_11.png)

上一节我们介绍了环境的安装，本节中我们来看看在数据分析中常用模块的基本使用。在整个数据分析领域，有三个核心模块需要掌握，它们被称为数据分析的“三剑客”。

![](img/2ec0c8051b77f5037cc4e18746533f6d_13.png)

以下是数据分析“三剑客”：
*   **NumPy模块**：用于高效的数值计算，是多维数组的基础。
*   **pandas模块**：用于数据处理和分析的核心工具，是课程的重点。
*   **matplotlib模块**：用于数据可视化，绘制图表。

我们整个课程的核心是学习pandas模块，NumPy和matplotlib模块虽然重要，但相对于pandas而言，其重要性稍低。NumPy模块的主要作用是为pandas模块提供数据源。

## NumPy模块初识 🧮

首先，我们来初步认识一下NumPy模块。

![](img/2ec0c8051b77f5037cc4e18746533f6d_15.png)

![](img/2ec0c8051b77f5037cc4e18746533f6d_17.png)

你可以将NumPy理解为一个**一维或多维的数组**。如果你有其他编程语言（如C、Java）的经验，你会知道数组的概念。如果只接触过Python，可以将数组暂时看作一种特殊的列表。无论是列表还是数组，它们都是一种**数据结构**，即一个可以装载数据的**容器**。

NumPy模块对应的容器就是**数组**，它可以装载相关数据。数组的“维度”可以这样理解：一维数组像一条线（如 `[1, 2, 3]`），二维数组像一个平面或表格（如 `[[1,2,3], [4,5,6]]`），三维及更高维度则以此类推。

NumPy数组与Python列表的关键区别在于：**NumPy数组要求其内部元素的数据类型必须统一**。如果你传入不同类型的数据，NumPy会自动将它们转换为同一类型。数据类型的转换优先级为：**字符串 > 浮点数 > 整数**。

NumPy模块的重点在于两点：
1.  它是一个容器，主要用于装载**数值型数据**并进行高效的数值运算。
2.  它是Python科学计算库的基础，内部封装了各种科学计算工具（如三角函数、统计函数等），我们可以直接调用这些工具对数组中的数据进行计算。

![](img/2ec0c8051b77f5037cc4e18746533f6d_19.png)

## 创建NumPy数组的三种方式 🛠️

![](img/2ec0c8051b77f5037cc4e18746533f6d_21.png)

![](img/2ec0c8051b77f5037cc4e18746533f6d_23.png)

介绍完NumPy模块后，接下来我们看看如何使用它。首先，你需要创建NumPy数组。

![](img/2ec0c8051b77f5037cc4e18746533f6d_25.png)

![](img/2ec0c8051b77f5037cc4e18746533f6d_27.png)

使用NumPy前需要导入模块，业界惯例是使用别名 `np`。

![](img/2ec0c8051b77f5037cc4e18746533f6d_29.png)

```python
import numpy as np
```

### 方式一：使用 `np.array()` 从现有数据创建

![](img/2ec0c8051b77f5037cc4e18746533f6d_31.png)

![](img/2ec0c8051b77f5037cc4e18746533f6d_33.png)

![](img/2ec0c8051b77f5037cc4e18746533f6d_35.png)

这是最直接的方式，可以将Python的列表、元组等序列类型转换为NumPy数组。

`np.array()` 函数的核心参数是 `object`，即数据源。我们可以传入一维或多维的列表来创建对应维度的数组。

![](img/2ec0c8051b77f5037cc4e18746533f6d_37.png)

以下是使用 `np.array()` 创建数组的示例：
```python
# 创建一维数组
arr1 = np.array([1, 2, 3])
print(arr1)  # 输出: [1 2 3]

![](img/2ec0c8051b77f5037cc4e18746533f6d_39.png)

# 创建二维数组
arr2 = np.array([[1, 2, 3], [4, 5, 6]])
print(arr2)
# 输出:
# [[1 2 3]
#  [4 5 6]]
```

### 方式二：使用 `plt.imread()` 从外部文件创建

![](img/2ec0c8051b77f5037cc4e18746533f6d_41.png)

![](img/2ec0c8051b77f5037cc4e18746533f6d_43.png)

![](img/2ec0c8051b77f5037cc4e18746533f6d_45.png)

我们可以将外部数据（如图片）读取到NumPy数组中进行处理。这需要用到“三剑客”中的matplotlib模块。

![](img/2ec0c8051b77f5037cc4e18746533f6d_47.png)

![](img/2ec0c8051b77f5037cc4e18746533f6d_49.png)

图片本质上是由二进制数据构成的，这些数据可以装载到NumPy数组中。通过改变数组中的数值，就能实现对图片的修改。

![](img/2ec0c8051b77f5037cc4e18746533f6d_51.png)

以下是读取图片并修改的示例：
```python
import matplotlib.pyplot as plt

![](img/2ec0c8051b77f5037cc4e18746533f6d_53.png)

![](img/2ec0c8051b77f5037cc4e18746533f6d_55.png)

![](img/2ec0c8051b77f5037cc4e18746533f6d_57.png)

![](img/2ec0c8051b77f5037cc4e18746533f6d_59.png)

# 1. 读取图片，数据存储在NumPy数组中
image_array = plt.imread(‘1.jpg‘) # 假设当前目录有1.jpg文件
print(image_array.shape) # 查看数组形状，例如 (高度, 宽度, 通道数)

![](img/2ec0c8051b77f5037cc4e18746533f6d_61.png)

![](img/2ec0c8051b77f5037cc4e18746533f6d_63.png)

# 2. 显示原始图片
plt.imshow(image_array)
plt.show()

![](img/2ec0c8051b77f5037cc4e18746533f6d_65.png)

![](img/2ec0c8051b77f5037cc4e18746533f6d_67.png)

![](img/2ec0c8051b77f5037cc4e18746533f6d_69.png)

# 3. 修改数组数值（例如，将所有像素值减去100，图片会变暗）
modified_array = image_array - 100
plt.imshow(modified_array)
plt.show()
```

![](img/2ec0c8051b77f5037cc4e18746533f6d_71.png)

![](img/2ec0c8051b77f5037cc4e18746533f6d_73.png)

### 方式三：使用NumPy内置函数快速创建

![](img/2ec0c8051b77f5037cc4e18746533f6d_75.png)

![](img/2ec0c8051b77f5037cc4e18746533f6d_77.png)

NumPy提供了一系列函数，可以快速创建具有特定规律的数组。

![](img/2ec0c8051b77f5037cc4e18746533f6d_79.png)

以下是几种常用的快速创建数组的函数：

![](img/2ec0c8051b77f5037cc4e18746533f6d_81.png)

![](img/2ec0c8051b77f5037cc4e18746533f6d_83.png)

*   **`np.zeros(shape)` / `np.ones(shape)`**：创建指定形状、元素全为0或全为1的数组。
    ```python
    np.zeros((3, 4)) # 创建3行4列的全0数组
    np.ones((2, 3))  # 创建2行3列的全1数组
    ```
*   **`np.linspace(start, stop, num)`**：在指定的区间内，创建包含`num`个等间距点的一维数组（等差数列）。
    ```python
    np.linspace(0, 100, 20) # 在0到100之间创建20个等间距的数
    ```
*   **`np.arange(start, stop, step)`**：类似于Python的`range()`，但返回的是NumPy数组。创建从`start`到`stop`（不包含），步长为`step`的一维数组。
    ```python
    np.arange(10, 50, 2) # 从10开始，到50结束（不含50），步长为2
    ```
*   **`np.random.randint(low, high, size)`**：创建指定形状`size`的数组，其元素是`[low, high)`区间内的随机整数。
    ```python
    np.random.randint(0, 100, (5, 3)) # 创建5行3列的数组，元素为0-99的随机整数
    ```

![](img/2ec0c8051b77f5037cc4e18746533f6d_85.png)

## 总结 📝

![](img/2ec0c8051b77f5037cc4e18746533f6d_87.png)

![](img/2ec0c8051b77f5037cc4e18746533f6d_89.png)

本节课中我们一起学习了NumPy模块的基础知识以及创建NumPy数组的三种核心方式。

![](img/2ec0c8051b77f5037cc4e18746533f6d_91.png)

![](img/2ec0c8051b77f5037cc4e18746533f6d_93.png)

![](img/2ec0c8051b77f5037cc4e18746533f6d_95.png)

![](img/2ec0c8051b77f5037cc4e18746533f6d_97.png)

我们首先认识了数据分析的“三剑客”——NumPy、pandas和matplotlib，并明确了NumPy作为数值计算容器和科学计算基础的角色。然后，我们深入探讨了三种创建数组的方法：
1.  **`np.array()`**：从现有Python数据结构（如列表）转换。
2.  **`plt.imread()`**：从外部文件（如图片）读取数据生成数组。
3.  **NumPy内置函数**：如`zeros`、`ones`、`linspace`、`arange`、`random.randint`等，用于快速生成具有特定规律的数组。

![](img/2ec0c8051b77f5037cc4e18746533f6d_99.png)

![](img/2ec0c8051b77f5037cc4e18746533f6d_101.png)

![](img/2ec0c8051b77f5037cc4e18746533f6d_103.png)

![](img/2ec0c8051b77f5037cc4e18746533f6d_105.png)

掌握这些创建数组的方法是后续进行数组索引、切片、运算以及更高级数据分析的基石。现在，你已经可以创建出装载着数值型数据的NumPy数组了。