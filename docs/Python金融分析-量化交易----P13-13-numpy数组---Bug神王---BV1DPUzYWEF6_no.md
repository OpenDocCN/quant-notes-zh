# Python金融分析+量化交易：P13：NumPy数组的三种创建方式 🚀

![](img/5f57eab21c99508484b27dc4288bbf0b_1.png)

![](img/5f57eab21c99508484b27dc4288bbf0b_3.png)

在本节课中，我们将学习数据分析的核心模块之一——NumPy。我们将重点探讨NumPy数组的三种主要创建方式，理解其作为数值计算容器的基本特性，并通过实际代码演示来加深理解。

![](img/5f57eab21c99508484b27dc4288bbf0b_5.png)

## 概述 📋

![](img/5f57eab21c99508484b27dc4288bbf0b_7.png)

![](img/5f57eab21c99508484b27dc4288bbf0b_9.png)

上一节我们介绍了环境的安装。本节中，我们来看看在数据分析中常用模块的基本使用。在整个数据分析领域，有三个核心模块需要掌握，它们被称为“数据分析三剑客”，分别是：**NumPy**、**pandas**和**matplotlib**。我们后续的所有数据分析操作都将围绕这三个模块展开。

![](img/5f57eab21c99508484b27dc4288bbf0b_11.png)

首先，我们来了解一下第一个核心模块：NumPy。

![](img/5f57eab21c99508484b27dc4288bbf0b_13.png)

## NumPy模块简介 🧮

NumPy模块是我们数据分析的重点操作之一。虽然pandas是核心，但NumPy为pandas提供了重要的数据源支持。理解NumPy是学习pandas的基础。

简单来说，NumPy可以理解为一个**一维或多维的数组**。如果你有其他编程语言（如C、Java）的经验，应该对数组不陌生。如果只接触过Python，可以将数组暂时视为一种特殊的列表，或者将列表视为一种特殊的数组。它们都是一种数据结构，即一个可以装载数据的容器。

NumPy数组这个容器主要装载**数值型数据**。NumPy的重点在于**数值计算**。在数据分析中，如果需要对一组数值进行运算，可以先将这些数值装载到NumPy数组中，再利用NumPy的特性进行高效计算。

![](img/5f57eab21c99508484b27dc4288bbf0b_15.png)

此外，NumPy是Python**科学计算库的基础**。这意味着NumPy内部已经封装好了各种科学计算所需的工具类。例如，如果需要对一组数据进行反三角函数运算，我们无需手动实现，直接调用NumPy中封装好的函数即可。

![](img/5f57eab21c99508484b27dc4288bbf0b_17.png)

总结NumPy的两个核心点：
1.  它是一个容器，用于装载和进行**数值型数据**的运算。
2.  它是Python科学计算的基础，提供了丰富的**科学计算工具类**。

## 创建NumPy数组 🛠️

介绍完NumPy的基本概念后，接下来我们看看如何创建NumPy数组。首先需要导入NumPy模块。

```python
import numpy as np
```
业界约定俗成地使用 `np` 作为NumPy的别名。导入模块后，我们就可以开始创建数组了。NumPy模块通常已集成在Anaconda等科学计算环境中，无需单独安装。

![](img/5f57eab21c99508484b27dc4288bbf0b_19.png)

### 方式一：使用 `np.array()` 从列表创建

![](img/5f57eab21c99508484b27dc4288bbf0b_21.png)

这是最直接的方式。我们可以通过 `np.array()` 函数，将Python列表（或嵌套列表）转换为NumPy数组。

![](img/5f57eab21c99508484b27dc4288bbf0b_23.png)

以下是创建一维和二维数组的示例：

![](img/5f57eab21c99508484b27dc4288bbf0b_25.png)

![](img/5f57eab21c99508484b27dc4288bbf0b_27.png)

```python
# 创建一维数组
arr1 = np.array([1, 2, 3])
print(arr1)

![](img/5f57eab21c99508484b27dc4288bbf0b_29.png)

# 创建二维数组
arr2 = np.array([[1, 2, 3], [4, 5, 6]])
print(arr2)
```
`np.array()` 函数的关键参数是 `object`（数据源）和可选的 `dtype`（数组元素的数据类型）。如果不指定 `dtype`，NumPy会自动推断输入数据的类型。

通过这种方式，我们可以创建任意维度的数组，但最常用的是**一维、二维和三维数组**。

![](img/5f57eab21c99508484b27dc4288bbf0b_31.png)

掌握了基本的数组创建方式后，我们来看一个关键问题：NumPy数组与Python列表的主要区别是什么？

![](img/5f57eab21c99508484b27dc4288bbf0b_33.png)

![](img/5f57eab21c99508484b27dc4288bbf0b_35.png)

**核心区别在于数组元素的类型一致性。** 列表可以容纳不同类型的元素，但NumPy数组要求所有元素必须是**相同类型**。如果传入的数据类型不一致，NumPy会按照一定规则（字符串 > 浮点数 > 整数）进行类型转换。

```python
# 尝试创建包含不同数据类型的数组
arr_mixed = np.array([1, 2.2, ‘three’])
print(arr_mixed)
print(arr_mixed.dtype) # 查看数组数据类型
```
你会发现，所有元素都被转换成了字符串类型。这体现了NumPy为高效数值计算而设计的特性。

![](img/5f57eab21c99508484b27dc4288bbf0b_37.png)

### 方式二：使用 `plt.imread()` 从图像文件创建

![](img/5f57eab21c99508484b27dc4288bbf0b_39.png)

NumPy数组作为容器，可以装载各种形式的数据，包括图像数据。图像本质上是由二进制数值构成的，因此可以加载到NumPy数组中。这里我们需要用到三剑客中的另一个模块：matplotlib。

以下是将外部图片读取到NumPy数组的示例：

![](img/5f57eab21c99508484b27dc4288bbf0b_41.png)

![](img/5f57eab21c99508484b27dc4288bbf0b_43.png)

```python
import matplotlib.pyplot as plt

![](img/5f57eab21c99508484b27dc4288bbf0b_45.png)

# 读取图片，数据存储在NumPy数组中
image_array = plt.imread(‘1.jpg’)
print(image_array.shape) # 查看数组形状，例如 (高度, 宽度, 通道数)

![](img/5f57eab21c99508484b27dc4288bbf0b_47.png)

![](img/5f57eab21c99508484b27dc4288bbf0b_49.png)

![](img/5f57eab21c99508484b27dc4288bbf0b_51.png)

# 显示原始图片
plt.imshow(image_array)
plt.show()
```
`plt.imread()` 函数会返回一个多维NumPy数组（通常是三维，代表图片的高度、宽度和颜色通道）。数组中存储的就是图片的像素数据。

![](img/5f57eab21c99508484b27dc4288bbf0b_53.png)

我们可以通过直接操作这个数组来改变图片。例如，将数组所有元素的值减去一个常数，相当于调整图片的亮度：

![](img/5f57eab21c99508484b27dc4288bbf0b_55.png)

![](img/5f57eab21c99508484b27dc4288bbf0b_57.png)

![](img/5f57eab21c99508484b27dc4288bbf0b_59.png)

```python
# 修改数组数据（例如，降低亮度）
modified_image = image_array - 50
# 确保值在合理范围内（对于图像数据通常是0-255）
modified_image = np.clip(modified_image, 0, 255)

![](img/5f57eab21c99508484b27dc4288bbf0b_61.png)

![](img/5f57eab21c99508484b27dc4288bbf0b_63.png)

# 显示修改后的图片
plt.imshow(modified_image.astype(‘uint8’)) # 转换回图像数据类型
plt.show()
```
这个例子揭示了图像处理软件（如美图秀秀）的部分原理：它们本质上是对代表图像的数值数组进行特定的数学运算和加工。

![](img/5f57eab21c99508484b27dc4288bbf0b_65.png)

![](img/5f57eab21c99508484b27dc4288bbf0b_67.png)

### 方式三：使用NumPy内置函数创建

![](img/5f57eab21c99508484b27dc4288bbf0b_69.png)

![](img/5f57eab21c99508484b27dc4288bbf0b_71.png)

![](img/5f57eab21c99508484b27dc4288bbf0b_73.png)

除了从现有数据转换，NumPy还提供了一系列函数来直接生成具有特定规律的数组。

![](img/5f57eab21c99508484b27dc4288bbf0b_75.png)

以下是几种常用的创建方法：

![](img/5f57eab21c99508484b27dc4288bbf0b_77.png)

*   **`np.zeros()` / `np.ones()`**：创建全为0或全为1的数组，需指定形状 `shape`。

    ```python
    # 创建3行4列的全零数组
    arr_zeros = np.zeros((3, 4))
    print(arr_zeros)

    # 创建2行3列的全一数组
    arr_ones = np.ones((2, 3))
    print(arr_ones)
    ```

![](img/5f57eab21c99508484b27dc4288bbf0b_79.png)

*   **`np.linspace()`**：创建**一维**的等差数列数组。参数为起始值、结束值和元素个数。

    ```python
    # 在0到100之间生成20个等间隔的数
    arr_lin = np.linspace(0, 100, 20)
    print(arr_lin)
    ```

![](img/5f57eab21c99508484b27dc4288bbf0b_81.png)

![](img/5f57eab21c99508484b27dc4288bbf0b_83.png)

*   **`np.arange()`**：类似Python的 `range()`，创建**一维**的等差数列数组。参数为起始值、结束值（不包含）和步长。

    ```python
    # 从10开始，到50结束（不包含50），步长为2
    arr_arange = np.arange(10, 50, 2)
    print(arr_arange)
    ```

![](img/5f57eab21c99508484b27dc4288bbf0b_85.png)

*   **`np.random.randint()`**：创建指定范围内的随机整数数组。

    ```python
    # 创建5行3列的数组，元素为0到100之间的随机整数
    arr_rand = np.random.randint(0, 100, size=(5, 3))
    print(arr_rand)
    ```

`np.zeros()` 和 `np.ones()` 可以创建任意维度的数组，而 `np.linspace()` 和 `np.arange()` 通常用于创建一维数组。

![](img/5f57eab21c99508484b27dc4288bbf0b_87.png)

![](img/5f57eab21c99508484b27dc4288bbf0b_89.png)

![](img/5f57eab21c99508484b27dc4288bbf0b_91.png)

## 总结 🎯

![](img/5f57eab21c99508484b27dc4288bbf0b_93.png)

![](img/5f57eab21c99508484b27dc4288bbf0b_95.png)

![](img/5f57eab21c99508484b27dc4288bbf0b_97.png)

![](img/5f57eab21c99508484b27dc4288bbf0b_99.png)

本节课我们一起学习了NumPy数组的三种核心创建方式：
1.  **`np.array()`**：从Python列表或嵌套列表转换，是最基础、最灵活的方式。
2.  **`plt.imread()`**：从图像文件读取数据，将图像像素值加载到多维数组中，是连接数据分析和图像处理的重要桥梁。
3.  **NumPy内置函数**：如 `zeros()`, `ones()`, `linspace()`, `arange()`, `random.randint()` 等，用于快速生成具有特定规律（如全零、等差、随机）的数组。

![](img/5f57eab21c99508484b27dc4288bbf0b_101.png)

![](img/5f57eab21c99508484b27dc4288bbf0b_103.png)

![](img/5f57eab21c99508484b27dc4288bbf0b_105.png)

我们还明确了NumPy数组与Python列表的关键区别：**NumPy数组要求元素类型一致**，这是其能够实现高效数值计算的前提。至此，我们已经掌握了创建NumPy数组的方法，为后续学习数组的索引、切片、运算等高级操作打下了坚实的基础。