# Python数据分析数据清洗，金融量化投资分析与股票交易实战：P3：03 numpy数组三种神秘创建方式 🧮

![](img/c2638dabd66f5108ef57b9130ad47994_1.png)

在本节课中，我们将要学习NumPy模块的基础知识，特别是如何创建NumPy数组。NumPy是Python数据分析“三剑客”之一，是进行科学计算和数值运算的核心库。

![](img/c2638dabd66f5108ef57b9130ad47994_3.png)

![](img/c2638dabd66f5108ef57b9130ad47994_4.png)

## 概述

NumPy模块的核心是一个称为“数组”的容器，它可以高效地存储和操作数值型数据。本节我们将重点介绍创建NumPy数组的三种主要方式。

![](img/c2638dabd66f5108ef57b9130ad47994_6.png)

![](img/c2638dabd66f5108ef57b9130ad47994_7.png)

![](img/c2638dabd66f5108ef57b9130ad47994_8.png)

## NumPy模块简介

![](img/c2638dabd66f5108ef57b9130ad47994_10.png)

上一节我们介绍了数据分析的环境配置，本节中我们来看看数据分析中常用的核心模块。在Python数据分析领域，有三个至关重要的模块，它们被称为“三剑客”：
*   **NumPy**
*   **Pandas**
*   **Matplotlib**

整个数据分析课程将围绕这三个模块展开。其中，**Pandas**是学习的重点和核心，而**NumPy**模块主要为Pandas提供底层的数据结构和数值计算支持。

NumPy模块可以理解为一个**一维或多维的数组**。如果你有其他编程语言经验，可以将其类比为数组；如果只接触过Python，可以暂时将其视为一种特殊的列表。它是一种数据结构，即一个用于装载数据的容器。

NumPy模块的重点在于两点：
1.  **数值计算**：它装载的数据主要是**数值型数据**，并提供了高效的数组运算能力。
2.  **科学计算基础**：它封装了丰富的科学计算工具类（如三角函数、统计函数等），方便我们直接调用。

## 创建NumPy数组的第一种方式：`np.array()`

![](img/c2638dabd66f5108ef57b9130ad47994_12.png)

![](img/c2638dabd66f5108ef57b9130ad47994_13.png)

要使用NumPy，首先需要导入模块，业界惯例是使用`np`作为别名。

```python
import numpy as np
```

导入模块后，就可以创建数组了。最直接的方式是使用`np.array()`函数。

以下是`np.array()`函数的基本用法示例：

```python
# 创建一维数组
arr_1d = np.array([1, 2, 3])
print(arr_1d)
# 输出: [1 2 3]

![](img/c2638dabd66f5108ef57b9130ad47994_15.png)

# 创建二维数组
arr_2d = np.array([[1, 2, 3], [4, 5, 6]])
print(arr_2d)
# 输出:
# [[1 2 3]
#  [4 5 6]]
```

![](img/c2638dabd66f5108ef57b9130ad47994_17.png)

![](img/c2638dabd66f5108ef57b9130ad47994_18.png)

`np.array()`函数的关键参数是`object`（数据源，如列表）和`dtype`（指定数组元素的数据类型）。如果不指定`dtype`，NumPy会自动推断。

![](img/c2638dabd66f5108ef57b9130ad47994_19.png)

![](img/c2638dabd66f5108ef57b9130ad47994_21.png)

### 数组与列表的一个重要区别

列表可以容纳不同类型的元素，但NumPy数组要求元素类型必须统一。如果传入的数据类型不一致，NumPy会按照**数据类型优先级**进行强制转换。

![](img/c2638dabd66f5108ef57b9130ad47994_23.png)

优先级顺序为：**字符串 > 浮点数 > 整数**。

```python
# 示例：混合类型会被统一转换
arr_mixed = np.array([1, 2.2, ‘three‘])
print(arr_mixed)
# 输出: [‘1‘ ‘2.2‘ ‘three‘] (全部转换为字符串)

![](img/c2638dabd66f5108ef57b9130ad47994_25.png)

![](img/c2638dabd66f5108ef57b9130ad47994_26.png)

![](img/c2638dabd66f5108ef57b9130ad47994_27.png)

arr_mixed2 = np.array([1, 2.2, 3.3])
print(arr_mixed2)
# 输出: [1.  2.2 3.3] (全部转换为浮点数)
```

## 创建NumPy数组的第二种方式：从外部数据读取（如图片）

数据装载到不同的容器（如列表、字典、数组）中，就可以利用该容器的特性对数据进行处理。NumPy数组的特性非常适合进行数值运算。

![](img/c2638dabd66f5108ef57b9130ad47994_29.png)

![](img/c2638dabd66f5108ef57b9130ad47994_30.png)

我们可以将外部文件（如图片）的数据读取到NumPy数组中，然后通过操作数组来改变原始数据。这里需要用到“三剑客”中的另一个模块：Matplotlib。

以下是读取图片并修改的示例：

```python
import matplotlib.pyplot as plt

![](img/c2638dabd66f5108ef57b9130ad47994_32.png)

# 1. 使用plt.imread()读取图片，数据被加载到NumPy数组中
image_array = plt.imread(‘1.jpg‘)  # 假设当前目录有1.jpg文件
print(image_array.shape)  # 查看数组形状，例如 (高度, 宽度, 通道数)，这是一个三维数组

![](img/c2638dabd66f5108ef57b9130ad47994_33.png)

![](img/c2638dabd66f5108ef57b9130ad47994_34.png)

![](img/c2638dabd66f5108ef57b9130ad47994_36.png)

# 2. 显示原始图片
plt.imshow(image_array)
plt.show()

![](img/c2638dabd66f5108ef57b9130ad47994_38.png)

![](img/c2638dabd66f5108ef57b9130ad47994_39.png)

# 3. 对数组进行数值运算（相当于处理图片数据）
modified_image_array = image_array - 100  # 所有像素值减去100

![](img/c2638dabd66f5108ef57b9130ad47994_41.png)

# 4. 显示处理后的图片
plt.imshow(modified_image_array)
plt.show()
```

![](img/c2638dabd66f5108ef57b9130ad47994_43.png)

这段代码演示了图片处理软件（如美图秀秀）的基本原理：**通过特定的算法对图片的像素数据（存储在数组中）进行运算，从而产生效果**。`plt.imread()`是创建NumPy数组的第二种方式。

![](img/c2638dabd66f5108ef57b9130ad47994_45.png)

![](img/c2638dabd66f5108ef57b9130ad47994_46.png)

## 创建NumPy数组的第三种方式：使用NumPy内置函数

![](img/c2638dabd66f5108ef57b9130ad47994_47.png)

![](img/c2638dabd66f5108ef57b9130ad47994_48.png)

![](img/c2638dabd66f5108ef57b9130ad47994_49.png)

NumPy提供了许多便捷的函数来快速生成具有特定规律的数组。

![](img/c2638dabd66f5108ef57b9130ad47994_51.png)

![](img/c2638dabd66f5108ef57b9130ad47994_52.png)

![](img/c2638dabd66f5108ef57b9130ad47994_53.png)

以下是几种常用的数组创建函数：

![](img/c2638dabd66f5108ef57b9130ad47994_54.png)

```python
# 1. 创建全0数组
zeros_arr = np.zeros((3, 4))  # 创建3行4列的全0二维数组
print(zeros_arr)

![](img/c2638dabd66f5108ef57b9130ad47994_56.png)

![](img/c2638dabd66f5108ef57b9130ad47994_57.png)

# 2. 创建全1数组
ones_arr = np.ones((2, 3))    # 创建2行3列的全1二维数组
print(ones_arr)

![](img/c2638dabd66f5108ef57b9130ad47994_59.png)

![](img/c2638dabd66f5108ef57b9130ad47994_61.png)

# 3. 创建等差数列数组 (一维)
linspace_arr = np.linspace(0, 10, 20)  # 在0到10之间生成20个等间隔的数
print(linspace_arr)

# 4. 创建等差间隔数组 (一维)
arange_arr = np.arange(10, 50, 2)  # 从10开始，到50结束（不含50），步长为2
print(arange_arr)

# 5. 创建随机数数组
random_arr = np.random.randint(0, 100, size=(5, 3))  # 创建5行3列的数组，元素为0-100间的随机整数
print(random_arr)
```

![](img/c2638dabd66f5108ef57b9130ad47994_63.png)

![](img/c2638dabd66f5108ef57b9130ad47994_64.png)

**`np.zeros()`和`np.ones()`**可以通过`shape`参数创建任意维度的数组。
**`np.linspace()`和`np.arange()`**主要用于生成一维的等差数列。
**`np.random`**下的函数（如`randint`）用于生成随机数数组。

## 总结

![](img/c2638dabd66f5108ef57b9130ad47994_66.png)

![](img/c2638dabd66f5108ef57b9130ad47994_67.png)

![](img/c2638dabd66f5108ef57b9130ad47994_68.png)

![](img/c2638dabd66f5108ef57b9130ad47994_69.png)

![](img/c2638dabd66f5108ef57b9130ad47994_70.png)

![](img/c2638dabd66f5108ef57b9130ad47994_71.png)

本节课中我们一起学习了创建NumPy数组的三种核心方式：
1.  **`np.array()`**：从现有数据（如列表、元组）直接转换。
2.  **从外部数据读取**：例如通过`plt.imread()`将图片文件读入数组。
3.  **使用NumPy内置函数**：如`np.zeros()`, `np.ones()`, `np.linspace()`, `np.arange()`, `np.random.randint()`等，快速生成具有特定规律或数值的数组。

![](img/c2638dabd66f5108ef57b9130ad47994_72.png)

![](img/c2638dabd66f5108ef57b9130ad47994_73.png)

![](img/c2638dabd66f5108ef57b9130ad47994_74.png)

![](img/c2638dabd66f5108ef57b9130ad47994_76.png)

我们还了解了NumPy数组与Python列表的一个关键区别：**数组元素类型必须统一**。掌握这些创建数组的方法是后续进行高效数值计算和数据分析的基础。